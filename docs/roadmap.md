# Roadmap

Timelines assume a small senior team (4-8 engineers) and a BaaS partner; regulatory onboarding often dominates the schedule.

## Phase 0: Foundations (weeks 0-6)
- Decide wedge, target market, and launch jurisdiction.
- Engage regulatory counsel; shortlist and start diligence on BaaS, card issuer, KYC vendor.
- Monorepo, CI/CD, infra-as-code, auth skeleton, design system.
- **Ledger package** with property-based tests (balance invariants, idempotency, concurrency).

## Phase 1: Sandbox MVP (weeks 6-16)
- Onboarding with KYC vendor (sandbox), account creation via partner sandbox.
- Ledger-backed accounts, balances, transaction feed, push notifications.
- Internal P2P transfers, bank transfer out/in via partner sandbox.
- Virtual card issuance (sandbox), freeze/unfreeze, card auth webhook + holds.
- Back-office console: customer view, KYC cases, manual adjustments with dual control.
- Security hardening, threat model, first external pen test.

## Phase 2: Closed beta with real money (weeks 16-30)
- Partner production approval, compliance programme (policies, transaction monitoring, SAR workflow).
- Staff/friends-and-family cohort with low limits; daily reconciliation live.
- Apple/Google Pay, physical cards, multi-currency wallets + FX.
- Fraud rules v1, customer support tooling, incident runbooks.

## Phase 3: Public launch (months 8-12)
- Waitlist -> staged rollout with raised limits.
- Budgets, pots, round-ups, subscription detection.
- Referral and growth loops; support SLAs; status page.

## Phase 4: Differentiate & expand (year 2+)
- AI money assistant, cash-flow forecasting.
- International transfers, savings/interest products, premium tiers.
- Investing (stocks/ETFs via broker partner), business accounts.
- Second market; begin EMI licence application to reduce partner dependency.

## Key risks
| Risk | Mitigation |
|---|---|
| Partner dependence/outage | Own ledger, adapter interfaces, second partner on roadmap |
| Regulatory delay | Start counsel + partner diligence in Phase 0 |
| Fraud/scam losses | Conservative limits, rules engine early, scam warnings |
| Ledger bugs (money errors) | Property-based tests, reconciliation, dual control, invariants checks |
| Unit economics | Interchange + FX + subscriptions model; track cost per active user from day one |

## Open decisions
1. Wedge segment and launch country.
2. BaaS partner and card issuer shortlist.
3. Team size/budget and funding runway.
4. Brand name and licence strategy timeline.
