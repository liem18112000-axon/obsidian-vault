---
title: "Map decline codes to failureCategory at the KlaraPay boundary, not by prose-parsing in luz_store"
created: 2026-07-24
type: argument
status: seedling
source: "session 2026-07-24, investigation recommendation"
tags: [luz-online-payment, payrexx, design-decision, taxonomy, LUZ-157476]
---

# Map decline codes to failureCategory at the KlaraPay boundary, not by prose-parsing in luz_store

For the decline-code taxonomy (LUZ-157476 / LUZ-157809), introduce the customer-friendly category **inside luz_online_payment**, not by parsing message prose in luz_store.

Concretely: add a `failureCategory` enum field to `KlaraTransactionRequest` (the response contract to luz_store), and populate it in `KlaraTransactionRequestConverter.convertToKlaraTransactionRequest` (and at the `TransactionTask` catch block for the ERROR/prose path). luz_store then only maps `failureCategory` -> localized string.

**Why here, not luz_store:** luz_online_payment is the last place the raw Payrexx transaction data is visible before it is flattened into a free-text `message`. Doing the mapping at this boundary means luz_store consumes a structured category instead of pattern-matching English sentences. Message-string parsing in luz_store is brittle and is explicitly the last resort to avoid.

Repo: luz_online_payment. Ticket: LUZ-157476 / epic LUZ-156281 (credit-card-only billing).

## Related

- [[Payrexx card declines reach luz_store as ERROR with prose, not DECLINED]]
- [[luz_online_payment silently drops Payrexx decline codes]]
