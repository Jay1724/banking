# Regulatory & compliance path

> Not legal advice. Engage a fintech regulatory counsel before taking customer money. Verify all partner/regulatory details below against current sources; they change.

Holding customer funds and issuing cards is regulated. There are three ways in, in increasing cost and control:

| Route | What it is | Time to market | Cost/control |
|---|---|---|---|
| **1. Banking-as-a-Service (BaaS)** | Partner bank/EMI holds the licence and ledger; you build UX on its APIs | Months | Low capex, you depend on partner, per-account/txn fees |
| **2. EMI / payments licence** | Own e-money licence (UK FCA / EU national regulator); safeguard funds at a bank | 12-24 months | Medium; you control more of the stack |
| **3. Full banking licence** | Deposits, lending, deposit insurance | Years (Revolut's UK licence took ~3 years, incl. a mobilisation period) | Highest capital + governance burden |

**Recommendation:** start on route 1 in one jurisdiction, design so the ledger/rails layer can be swapped, and pursue route 2 once you have traction.

## Partner categories (evaluate several; examples, not endorsements)
- **BaaS / accounts:** e.g. Griffin, Modulr, Railsr (UK); Swan, Solaris (EU); Unit, Column, Treasury Prime (US).
- **Card issuing & processing:** e.g. Marqeta, Lithic, Adyen Issuing, Stripe Issuing.
- **KYC/KYB & AML screening:** e.g. Sumsub, Onfido, Veriff, ComplyAdvantage.
- **Open banking / account aggregation:** e.g. TrueLayer, Plaid, Tink.
- **FX liquidity:** e.g. Currencycloud, Wise Platform, bank FX desks.

## Obligations you will own regardless of route
- KYC/CDD, sanctions & PEP screening, transaction monitoring, SAR/STR filing.
- Safeguarding of customer funds (EMI) or deposit protection (bank).
- PSD2 / SCA (EU/UK) strong customer authentication.
- PCI DSS (if you touch card data; use issuer-hosted card display to minimise scope).
- GDPR/UK GDPR, data residency, retention.
- Complaints handling, consumer duty, fair-treatment of vulnerable customers.
- Operational resilience, incident reporting, audited controls (SOC 2 / ISO 27001).
