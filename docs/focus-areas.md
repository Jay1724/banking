# Focus areas: onboarding/KYC, credit pre-qualification, payments, fraud

> Draft. Vendor names, regulatory specifics and Standard Bank's internal systems are from general knowledge and must be verified. We have NOT seen the bank's architecture; the "legacy" assumptions below come from the pitch brief and must be validated in Discovery.

## Core thesis: an orchestration layer, not a rip-and-replace
Legacy cores are slow to change but rarely need to. Put a thin **anti-corruption / orchestration layer** between the app and the legacy systems:

```
 App (RN/native) ─▶ Experience API (BFF) ─▶ Orchestrator (workflows, retries, caching, audit)
                                              ├─ Identity adapter   ─▶ DHA/NPR verification, liveness, doc OCR
                                              ├─ Credit adapter     ─▶ bureau(s) soft enquiry, affordability
                                              ├─ Fraud adapter      ─▶ device, SIM-swap, behavioural signals
                                              ├─ Payments adapter   ─▶ core, PayShap/RTC/EFT rails
                                              └─ Core banking adapter (legacy, behind a stable modern API)
```
Benefits: each legacy dependency is isolated behind an interface, so we can add timeouts, caching, fallbacks, async/"we'll notify you" patterns, and later swap the backend without touching the app. Event streaming from the core (CDC/outbox) feeds real-time features.

## 1. Onboarding & KYC (the flagship)
**Goal:** open a fully verified account on a phone in minutes, with high completion and low manual review.

Target flow: phone number + OTP -> ID capture (SA smart ID/green book/passport) with OCR -> selfie + liveness -> **identity verification against Home Affairs (NPR/HANIS-style source via an authorised channel) [VERIFY]** -> address/proof of income capture as risk requires -> risk scoring and sanctions/PEP screening -> account number issued, card provisioned to wallet instantly.

How we deal with the legacy verification problem:
- **Asynchronous, resumable verification.** Persist the application state; if the DHA/legacy lookup is slow or down, the customer gets a "verifying, we'll notify you" state with push/SMS, not an error. Idempotent retries with backoff.
- **Tiered account activation.** Allow limited functionality (low limits) immediately after a passing liveness + document check, upgrading to full limits once source-of-truth verification completes (consistent with FICA's risk-based approach **[VERIFY with compliance]**).
- **Multi-source corroboration.** Combine document OCR, face match, bureau header data and the DHA lookup so a single flaky source doesn't block good customers; route only genuinely ambiguous cases to manual review with a case tool.
- **Drop-off analytics.** Step-level funnel, per-step latency, per-device failure rates; this is where the revenue is. Instrument from day one.
- **Inclusion:** low-end Android, low data, multi-language, assisted/branch-handoff mode, guidance on image capture.
- **Re-use:** existing-to-bank customers skip steps (open a second account/product in one tap).

Success metrics: onboarding completion rate, median time-to-account, % auto-approved, manual-review rate, cost per verified customer, 30-day activation (first transaction / salary in).

## 2. Credit pre-qualification (soft pull, "what can I afford?")
**Goal:** show a customer in-app, with consent, an indicative credit limit/offer without hurting their score, then convert to a product.

- Use a **soft enquiry** with explicit POPIA-compliant consent **[VERIFY: bureau product, NCA treatment of enquiries]**, plus the bank's own data (income, salary-in, balances, repayment behaviour) for an *indicative* offer.
- Clearly label as indicative; a formal NCA-compliant **affordability assessment** still precedes any actual credit grant **[VERIFY with credit risk/legal]**.
- Architecture: credit adapter queries one or more bureaus, normalises responses, caches within a permitted window, and feeds a decision engine (rules first, ML later) owned/approved by the bank's credit risk. Every decision is explainable and logged.
- UX: a simple "you may qualify for up to R X" with a drivers breakdown, a what-if slider (term/amount), and one-tap apply with pre-filled data.
- Revenue link: pre-approved offers (personal loan, overdraft, credit card, vehicle/home finance leads) drive origination volume and cross-sell; track offer view -> apply -> approve -> disburse.
- Guardrails: responsible-lending, no dark patterns, over-indebtedness checks, clear fees/rates.

## 3. Payments flows
- Pay by phone number / PayShap-style instant payments with confirm-payee display and clear fee before commit **[VERIFY rails and bank capability]**.
- Saved beneficiaries, request-money, split bills, scheduled/recurring payments, QR payments, instant receipts and shareable proof of payment.
- Smart failure handling: pending/failed/reversed states explained in plain language, one-tap retry, status tracking.
- Real-time balances and notifications (event-driven), cutting "where's my money?" calls.
- Card payments: instant freeze, limits, tap-to-pay/wallet, virtual cards for online spending.
- Revenue: payment volume and interchange, fee-optimised routing, FX/cross-border to Africa corridors, merchant/business payment links as a later line.

## 4. Fraud prevention
SA-specific threats to design for **[VERIFY with the bank's fraud team / SABRIC data]**: SIM-swap, account takeover, social-engineering/APP scams, fake-beneficiary/invoice fraud, synthetic and stolen identities at onboarding, card-not-present.
- **Device + behavioural signals:** device binding, jailbreak/root and overlay/remote-access detection, typing/touch biometrics, impossible-travel.
- **SIM-swap / number-change signals** from the operator/aggregator or recent-change checks, triggering step-up auth and cooling-off limits on new devices/beneficiaries **[VERIFY availability]**.
- **Real-time risk scoring on every payment** (new payee + high amount + new device + unusual hour), with graded responses: allow, warn ("is this a scam?"), step-up, hold for review, block.
- **Contextual scam warnings** at the moment of risk; cooling-off for first-time large payments.
- **Onboarding fraud:** liveness/deepfake detection, document tamper checks, cross-application link analysis.
- **Feedback loop:** analyst decisions and confirmed fraud retrain rules/models; case management and customer-friendly dispute flows.
- Metrics: fraud loss (bps), scam-intercept rate, false-positive rate/friction, time-to-decision.

## 5. How it grows revenue (link every feature to a metric)
| Lever | Mechanism | KPI |
|---|---|---|
| More customers | Higher onboarding completion, lower CAC | Completion %, cost per account |
| Faster activation | Instant card, salary-switch prompts | 30-day activation, primary-account rate |
| Credit origination | Pre-qualified offers in-app | Offer->apply->disburse conversion, NII |
| Payment volume | Better flows, request/split/recurring | Txns per active, interchange/fees |
| Cross-sell | Insights-triggered offers (savings, insurance, investments) | Products per customer |
| Lower cost | Self-service, fewer calls/branch visits | Contacts per customer |
| Lower losses | Fraud interception, fewer false declines | Fraud bps, declined-good rate |

## 6. Discovery questions (ask the bank, and use the screenshots to answer some ourselves)
- What does the onboarding funnel look like step by step today, and where is the biggest drop-off? Average time? Manual-review %?
- How is DHA verification integrated (direct, via a vendor/aggregator, batch)? SLAs, error rates, cost per lookup?
- Which bureaus are used and under what agreements? Existing pre-approved-offer engine?
- Core banking and integration landscape: APIs, event streams, mainframe batch windows, change-freeze rules?
- Current fraud stack, loss figures, and what analysts wish they had?
- Governance: third-party risk onboarding, security reviews, data residency, cloud policy.

## 7. What to build for the pitch demo (once screenshots arrive)
1. **Onboarding prototype**: capture ID -> liveness -> "verifying" resumable state -> instant virtual card; show a simulated slow DHA lookup handled gracefully.
2. **Credit pre-qualification screen**: consent -> indicative limit with drivers -> apply.
3. **Payment risk-warning flow**: new-payee scam warning and step-up.
4. A one-slide **funnel model** showing revenue uplift from, e.g., +X pts completion (use the bank's real numbers when available).
