# System architecture

## Guiding decisions
1. **Own the ledger.** Even on a BaaS partner, keep your own double-entry ledger as the product source of truth and reconcile against the partner daily. This is what lets you switch partners and offer features (pots, instant spend limits) the partner doesn't.
2. **Modular monolith first, services later.** Start with one deployable TypeScript codebase with strict module boundaries (identity, ledger, payments, cards, kyc, notifications). Split out only the parts that need independent scaling/isolation (ledger, card auth).
3. **Event-driven.** State changes emit immutable events (outbox pattern -> Kafka/SQS) for notifications, analytics, fraud, and search.
4. **Idempotency everywhere** for money-moving APIs (client-supplied idempotency keys, unique constraints).
5. **Money is integers.** Store minor units (bigint) plus ISO-4217 currency; never floats.

## Components
```
 Mobile (React Native/Expo) ── HTTPS/TLS pinning ──▶ API Gateway / BFF
                                                      │
        ┌──────────────┬───────────────┬──────────────┼───────────────┬──────────────┐
     Identity/Auth   KYC/Onboarding   Accounts      Payments        Cards         Insights
     (sessions,      (vendor SDK,     & Ledger      (orchestrator,  (issuer       (categorise,
      devices, MFA)   case mgmt)      (double-      rails adapters, adapter,      budgets,
                                      entry)        FX, limits)     auth webhook) forecasts)
        └──────────────┴───────┬───────┴──────────────┴───────────────┴──────────────┘
                               ▼
              PostgreSQL (primary, PITR)   Event bus (Kafka/SQS)   Redis (rate limit, cache)
                               │                    │
                       Reconciliation        Fraud/AML engine, Notifications, Data warehouse
```

### Mobile
- React Native + Expo (EAS builds), TypeScript, React Query, secure storage (Keychain/Keystore), biometric unlock, device binding, jailbreak/root detection, certificate pinning.
- Offline-tolerant read views; money actions require online + server confirmation.
- Feature flags and remote config for staged rollout.

### Backend
- Node.js + TypeScript (NestJS or Fastify), Zod/OpenAPI schemas, Prisma/Kysely with hand-reviewed SQL for ledger paths.
- Async work via a durable queue (BullMQ/SQS); outbox table for reliable event publication.
- Auth: short-lived JWT access + rotating refresh tokens bound to device; step-up auth (SCA) for sensitive actions.

### Ledger (the critical piece)
Double-entry, append-only.
```
accounts(id, owner_id, type, currency, status)
journal_entries(id, idempotency_key UNIQUE, kind, created_at, metadata)
postings(id, entry_id, account_id, amount_minor BIGINT, direction CHECK in (debit,credit))
-- invariant: per entry, sum(debits) = sum(credits) per currency (enforced in txn + checked by job)
balances(account_id, available_minor, pending_minor, version)  -- derived/cached, updated in same txn
holds(id, account_id, amount_minor, expires_at, ...)           -- card authorisations
```
- Serializable (or row-lock + version check) transactions for balance-affecting writes.
- Corrections are new reversing entries; never UPDATE/DELETE postings.
- Nightly reconciliation vs partner/bank statements; breaks raise alerts.

### Payments orchestration
- Rail adapters behind one interface (Faster Payments/SEPA/ACH/Swift/card/internal book transfer).
- State machine per payment: `created -> screened -> authorised -> submitted -> settled | failed | returned`.
- Limits, velocity checks, sanctions screening, and fraud scoring run before submission.
- Internal (on-us) transfers settle instantly in the ledger.

### Cards
- Issuer-processor adapter; authorisation webhook answered in <~2s from a hot balance cache + hold creation.
- Card PAN/CVV never touch your servers; use issuer's secure display SDK to stay out of most PCI scope.

### Fraud, risk & AML
- Rules engine first (velocity, geo, device change, new payee + high amount), ML scoring later.
- Transaction monitoring + case management for the compliance team; audit trail on every decision.

### Insights / AI
- Merchant enrichment + categorisation (start with vendor enrichment + rules, add ML).
- AI assistant uses read-only tools over the user's own data; any money movement is a proposal the user confirms with SCA. Treat LLM output as untrusted; never let a model authorise payments.

## Security baseline
- Encryption in transit (TLS 1.2+) and at rest; field-level encryption for PII; KMS-managed keys.
- Secrets in a manager; no secrets in repo. Least-privilege IAM, prod access via audited break-glass.
- Immutable audit log; dual control for admin actions on money/customers.
- Rate limiting, bot protection, WAF; dependency and container scanning in CI; regular pen tests.
- Backups with PITR, tested restores, multi-AZ; defined RPO/RTO.

## Infrastructure
- Cloud (AWS or GCP), Terraform, Kubernetes or ECS Fargate, managed Postgres (Multi-AZ), managed Kafka/SQS.
- Environments: dev, staging (partner sandbox), prod. CI/CD with required reviews, trunk-based, canary deploys.
- Observability: OpenTelemetry traces, structured logs (PII-scrubbed), SLO-based alerting, on-call.

## Suggested repo layout (monorepo)
```
apps/mobile/        # Expo app
apps/api/           # modular monolith
apps/backoffice/    # internal admin / compliance console (Next.js)
packages/ledger/    # ledger domain + tests (property-based)
packages/shared/    # types, zod schemas, money utils
infra/              # terraform
docs/
```
