# Pitch: elevating the Standard Bank app

> Draft for discussion. Facts about Standard Bank's products, customer numbers and regulation are from general knowledge and MUST be verified (and quantified) before this goes to anyone. Items marked **[VERIFY]** or **[DATA]** are placeholders.

## 1. The one-line pitch
We build the experience layer that lets Standard Bank match the best neobank apps on speed, transparency and money intelligence, **on top of its existing licence, core banking and trust**, without a core replacement.

## 2. Why now
- South African customers compare their bank app with global neobanks and with local challengers (e.g. TymeBank, Discovery Bank, Capitec's app) **[VERIFY competitive set]**.
- Instant payments are arriving at scale (PayShap) **[VERIFY status/volumes]**, shifting expectations to real-time, low-fee, phone-number-based payments.
- Cost-to-serve and fraud pressure push banks toward self-service and smarter detection.

## 3. The opportunity (to be sized)
| Lever | Hypothesis | Metric to prove |
|---|---|---|
| Engagement | A real-time, categorised feed + budgets + pots raises weekly active use | WAU/MAU, sessions/week **[DATA]** |
| Primary-bank share | Better money tools make the app the customer's "main" account | Salary-in / primary-account rate **[DATA]** |
| Cost to serve | Self-service card controls, disputes and limits cut call-centre volume | Contacts per customer **[DATA]** |
| Fraud / scams | Behavioural warnings and confirm-payee style checks cut losses | Fraud loss bps, scam-intercept rate **[DATA]** |
| Fee transparency / FX | Clear pre-commit fees build trust and drive cross-border use | Conversion on FX/transfers **[DATA]** |
| Youth / acquisition | A modern experience wins first-account customers | New-to-bank acquisition, CAC **[DATA]** |

## 4. What we'd build (experience layer, not a new bank)
1. **Instant money feed**: real-time notifications, merchant-enriched, categorised transactions.
2. **Pots & goals**: sub-accounts, round-ups, automated saving rules.
3. **Card control centre**: freeze, limits, online/abroad toggles, virtual and disposable cards, wallet provisioning.
4. **Pay by phone number / PayShap** with clear fee display and request-money.
5. **Spend intelligence**: budgets, subscription detection, cash-flow forecast; later an AI assistant that proposes actions the customer confirms (never moves money autonomously).
6. **Security by design**: device binding, biometrics, scam warnings, instant lock.
7. **Low-data / offline-tolerant UX** for varied connectivity and entry-level Android devices, with multi-language support (e.g. English, isiZulu, Afrikaans) **[VERIFY priorities]**.

## 5. How it integrates (bank-grade, low-risk)
- **No core replacement.** We consume the bank's APIs/events; the bank's core ledger stays the system of record.
- Our service keeps a read-optimised projection (pots, categories, insights) and calls the bank for every money movement.
- Deployed in the bank's cloud/tenancy or on-prem, under its security, data-residency and change-management controls.
- Technology: React Native or native-module SDK embeddable in the existing app, so rollout can be feature-by-feature behind flags to a % of users.
- See [architecture.md](architecture.md) (the standalone-bank ledger design is optional here; the bank's core replaces it).

## 6. Regulatory & risk posture (South Africa) **[VERIFY with counsel/bank compliance]**
- The bank remains the regulated entity; we operate as a vendor/technology partner under its third-party-risk and outsourcing governance.
- Relevant regimes: POPIA (personal data), FICA (KYC/AML), Prudential Authority/SARB requirements incl. outsourcing and cloud guidance, FSCA/conduct and TCF-type outcomes, PASA/payment-system rules, PCI DSS for card data.
- Data residency, audit rights, exit/step-in plans, pen-testing and SOC 2/ISO 27001 roadmap are table stakes.

## 7. Engagement model: de-risked, staged
| Stage | Duration | Outcome |
|---|---|---|
| Discovery | 4-6 wks | Journey audit, analytics review, API landscape, success metrics agreed |
| Prototype | 6-8 wks | Clickable + working prototype on sandbox data; user testing with real customers |
| Pilot | 3-4 mths | 1-5% of users, 2-3 features, A/B against control, security review passed |
| Scale | ongoing | Staged rollout, SLAs, roadmap co-ownership |

Commercial options to discuss: fixed-fee discovery/prototype, then per-active-user or outcome-linked fees for pilot/scale. Pilot success criteria are agreed up front.

## 8. Why us
**[Your team, track record, credentials, references]**: this is the weakest part today. A bank will weigh delivery capability and security maturity heavily; consider a named senior technical lead and an advisor with bank/regulatory experience.

## 9. The ask
A 60-minute working session with the digital/retail product and technology leads to agree a Discovery scope and success metrics.

## 10. Before you pitch: homework
1. Teardown of the current Standard Bank app (screenshots, journeys, app-store reviews, performance on low-end devices). Real pain points beat generic claims.
2. Competitor teardown (Capitec, TymeBank, Discovery Bank, Revolut-style features) with a gap matrix.
3. Find the right sponsor: the bank's digital/innovation units and any existing fintech partnership or accelerator channels **[VERIFY routes]**. Cold pitches to a bank rarely work; a warm intro or pilot-friendly unit does.
4. Build a small, polished prototype of 2-3 hero features; a demo outperforms slides.
5. Prepare a security/compliance one-pager and answers to a standard third-party risk questionnaire.
6. Check IP/naming: avoid implying affiliation with Standard Bank or Revolut in public material.
