---
title: "LUZ-157476 decline taxonomy maps codes at luz_online_payment boundary"
created: 2026-07-23
type: model
status: seedling
source: "LUZ-157476 ticket + investigation 2026-07-23"
tags: [luz-store, payrexx, invoice-run, taxonomy, design-decision]
---

# LUZ-157476 decline taxonomy maps codes at luz_online_payment boundary

LUZ-157476 AC: Payrexx decline codes must map to five customer-friendly categories — insufficient-funds, card-expired, blocked-by-issuer, invalid-details, other (fallback, keeps taxonomy extensible). Raw provider codes never shown; the category is routed into both the failure email and the in-app message; display strings localized per category.

Design decision (draft, investigation report §6): do the code→category mapping inside **luz_online_payment**, where the raw Payrexx/ISO 8583 code is visible, and ship a category enum in the KlaraTransactionRequest response. luz_store then only does category→localized string. Parsing prose message strings in luz_store would be brittle — last resort only.

Evidence for why: production failure messages observed in the invoice-run UI are English prose ('An error occurred: Your card has expired.', 'Charge of pre-authorization failed.') — Payrexx's raw codes are already consumed upstream and never reach luz_store.

Open question: does Payrexx deliver the ISO code as a structured field to luz_online_payment, or only prose? Must confirm with processor (task 2.1) before freezing the mapping.

## Related
- [[DECLINED status falls through invoice charge-failure handling in luz_store]]
- [[Payrexx decline codes are ISO 8583 issuer codes from the card-issuing bank]]
- [[luz_store TransactionStatus mirrors Payrexx API statuses plus two Klara-only values]]
