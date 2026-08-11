---
ai_hash: e952f9e5e7be3949
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
aliases:
- Map decline codes to failureCategory at the KlaraPay boundary, not by prose-parsing
  in luz_store
created: 2026-07-23
entities:
- LUZ-157476
- decline taxonomy
- luz_online_payment
- Payrexx
- decline codes
- customer-friendly categories
- insufficient-funds
- card-expired
- blocked-by-issuer
- invalid-details
- other (category)
- raw provider codes
- failure email
- in-app message
- display strings
- luz_store
- code→category mapping
- ISO 8583 data
- failureCategory enum field
- KlaraTransactionRequest
- response contract
- KlaraTransactionRequestConverter.convertToKlaraTransactionRequest
- TransactionTask catch block
- ERROR/prose path
- localized string
- raw Payrexx transaction data
- free-text message
- message-string parsing
- production failure messages
- English prose
- raw codes
- ISO code
- structured field
- LUZ-157476 maps failure categories in luz_store only, overriding the boundary recommendation
- LUZ-157476 decline-code flow luz-online-payment forwards, luz_store maps
- LUZ-157809
- LUZ-156281
- credit-card-only billing
- DECLINED status falls through invoice charge-failure handling in luz_store
- Payrexx card declines reach luz_store as ERROR with prose, not DECLINED
- luz_online_payment silently drops Payrexx decline codes
- Payrexx ISO 8583 decline code to meaning reference table
- luz_store TransactionStatus mirrors Payrexx API statuses plus two Klara-only values
source: LUZ-157476 ticket + investigation 2026-07-23/24 (§6 recommendation)
status: seedling
tags:
- luz-store
- luz-online-payment
- payrexx
- invoice-run
- taxonomy
- design-decision
- LUZ-157476
title: LUZ-157476 decline taxonomy maps codes at luz_online_payment boundary
type: argument
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

%% ai-graph-start %%

**Related notes:**
- [[LUZ-157476 decline-code flow luz-online-payment forwards, luz_store maps]]
- [[LUZ-157476 maps failure categories in luz_store only, overriding the boundary recommendation]]
- [[Payrexx card declines reach luz_store as ERROR with prose, not DECLINED]]
- [[LUZ-157476 proposes seven failure categories with per-channel customer copy]]
- [[luz_online_payment silently drops Payrexx decline codes]]

**Relations:**
- LUZ-157476 — *concerns* — decline taxonomy
- decline taxonomy — *maps* — decline codes
- decline codes — *at* — luz_online_payment boundary
- Payrexx — *generates* — decline codes
- decline codes — *map to* — customer-friendly categories
- customer-friendly categories — *include* — insufficient-funds
- customer-friendly categories — *include* — card-expired
- customer-friendly categories — *include* — blocked-by-issuer
- customer-friendly categories — *include* — invalid-details
- customer-friendly categories — *include* — other (category)
- raw provider codes — *are* — never shown
- customer-friendly categories — *routed into* — failure email
- customer-friendly categories — *routed into* — in-app message
- display strings — *localized per* — customer-friendly categories
- code→category mapping — *recommended inside* — luz_online_payment
- luz_online_payment — *has access to* — raw Payrexx/ISO 8583 data
- Recommendation — *is to add* — failureCategory enum field
- failureCategory enum field — *to* — KlaraTransactionRequest
- KlaraTransactionRequest — *is* — response contract
- response contract — *for* — luz_store
- failureCategory enum field — *populated in* — KlaraTransactionRequestConverter.convertToKlaraTransactionRequest
- failureCategory enum field — *populated at* — TransactionTask catch block
- luz_store — *maps* — failureCategory enum field
- failureCategory enum field — *to* — localized string
- luz_online_payment — *is last place for* — raw Payrexx transaction data
- raw Payrexx transaction data — *flattened into* — free-text message
- message-string parsing — *in* — luz_store
- message-string parsing — *is* — brittle
- production failure messages — *are* — English prose
- raw codes — *do not reach* — luz_store
- Payrexx — *delivers* — ISO code
- ISO code — *as* — structured field
- LUZ-157476 — *superseded by* — LUZ-157476 maps failure categories in luz_store only, overriding the boundary recommendation
- LUZ-157476 — *final split in* — LUZ-157476 decline-code flow luz-online-payment forwards, luz_store maps
- LUZ-157476 — *is a* — Ticket
- LUZ-157809 — *is a* — Ticket
- LUZ-156281 — *is an* — epic
- LUZ-156281 — *concerns* — credit-card-only billing
- LUZ-157476 — *related to* — DECLINED status falls through invoice charge-failure handling in luz_store
- LUZ-157476 — *related to* — Payrexx card declines reach luz_store as ERROR with prose, not DECLINED
- LUZ-157476 — *related to* — luz_online_payment silently drops Payrexx decline codes
- LUZ-157476 — *related to* — Payrexx ISO 8583 decline code to meaning reference table
- LUZ-157476 — *related to* — luz_store TransactionStatus mirrors Payrexx API statuses plus two Klara-only values

%% ai-graph-end %%