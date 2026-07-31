---
title: "LUZ-157476 decline taxonomy maps codes at luz_online_payment boundary"
aliases:
  - "Map decline codes to failureCategory at the KlaraPay boundary, not by prose-parsing in luz_store"
created: 2026-07-23
type: argument
status: seedling
source: "LUZ-157476 ticket + investigation 2026-07-23/24 (§6 recommendation)"
tags: [luz-store, luz-online-payment, payrexx, invoice-run, taxonomy, design-decision, LUZ-157476]
---

# LUZ-157476 decline taxonomy maps codes at luz_online_payment boundary

**AC (LUZ-157476):** Payrexx decline codes map to five customer-friendly categories — insufficient-funds, card-expired, blocked-by-issuer, invalid-details, other (fallback, keeps the taxonomy extensible). Raw provider codes are never shown; the category is routed into both the failure email and the in-app message, with display strings localized per category.

**Recommendation (investigation report §6):** do the code→category mapping inside **luz_online_payment**, where the raw Payrexx/ISO 8583 data is still visible, not by parsing prose in luz_store. Concretely: add a `failureCategory` enum field to `KlaraTransactionRequest` (the response contract to luz_store) and populate it in `KlaraTransactionRequestConverter.convertToKlaraTransactionRequest` **and** at the `TransactionTask` catch block (the ERROR/prose path). luz_store then only maps `failureCategory` → localized string.

**Why here:** luz_online_payment is the last place the raw Payrexx transaction data exists before it is flattened into a free-text `message`. Message-string parsing in luz_store is brittle and explicitly the last resort — production failure messages are English prose ("An error occurred: Your card has expired.", "Charge of pre-authorization failed.") because the raw codes are consumed upstream and never reach luz_store.

**Open question at the time:** does Payrexx deliver the ISO code as a structured field at all (task 2.1)? Must be confirmed before freezing the mapping.

> [!warning] Superseded
> Scope was later reversed — see [[LUZ-157476 maps failure categories in luz_store only, overriding the boundary recommendation]], and the final split in [[1 Projects/luz_store/LUZ-157476/LUZ-157476 decline-code flow luz-online-payment forwards, luz_store maps]].

Ticket: LUZ-157476 / LUZ-157809, epic LUZ-156281 (credit-card-only billing).

## Related

- [[DECLINED status falls through invoice charge-failure handling in luz_store]]
- [[Payrexx card declines reach luz_store as ERROR with prose, not DECLINED]]
- [[luz_online_payment silently drops Payrexx decline codes]]
- [[3 Resources/Work-Kepler/Payrexx/Payrexx ISO 8583 decline code to meaning reference table]]
- [[luz_store TransactionStatus mirrors Payrexx API statuses plus two Klara-only values]]
