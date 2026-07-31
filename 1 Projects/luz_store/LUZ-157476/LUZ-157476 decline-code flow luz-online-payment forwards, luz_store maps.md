---
ai_hash: 91c0afe3f6d5bffd
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-29
entities:
- LUZ-157476
- decline-code flow
- luz-online-payment
- luz_store
- FailureCategory
- Payrexx
- ISO 8583 two-digit decline code
- message
- Stripe
- Transaction
- declineCode
- KlaraTransactionRequest
- taxonomy
- FailureCategory enum
- FailureCategory.map(...)
- prose keyword-matching
- DECLINED transaction
- success path
- KlaraTransactionRequestConverter
- wrapper-error
- TransactionTask
- Payrexx API
- JSON field name
- Jackson @JsonAnySetter
- Capture an unknown-named JSON field with Jackson @JsonAnySetter
source: session 2026-07-29; luz_store/docs/luz-157476/PAYREXX-RESPONSE.md
status: seedling
tags:
- luz-157476
- payrexx
- klarapay
- failure-category
- online-payment
title: 'LUZ-157476 decline-code flow: luz-online-payment forwards, luz_store maps'
type: decision
---

# LUZ-157476 decline-code flow: luz-online-payment forwards, luz_store maps

For credit-card charge/refund failures, the customer-friendly failure taxonomy (`FailureCategory`) is keyed off Payrexxs **ISO 8583 two-digit decline code** (e.g. "51", "05", "14") because it is stable and language-independent, unlike the free-text `message` (which arrives in multiple languages and even leaks raw Stripe/Payrexx internals).

Ownership split across the two repos:
- **luz-online-payment** (producer) *extracts* the code from the Payrexx `Transaction` response and *forwards* it as a new `declineCode` field on its `KlaraTransactionRequest`. It does NOT map to a category — no taxonomy lives here.
- **luz_store** (consumer) owns the `FailureCategory` enum and `FailureCategory.map(...)`, which will key off `declineCode` (keeping prose keyword-matching as a fallback for old/codeless transactions).

Two data paths reach luz_store: the in-band DECLINED transaction goes through the success path / `KlaraTransactionRequestConverter` (message null, code present), while a wrapper-error goes through the `TransactionTask` catch block (prose message, no `Transaction` object so no structured code).

Open dependency (2026-07-29): the exact Payrexx **API** JSON field name for the code is unconfirmed (Payrexx support said it shows in the back-office detail view; we asked whether/where it appears in the API response). Hence the capture uses [[Capture an unknown-named JSON field with Jackson @JsonAnySetter]]. luz_stores consumer-side `KlaraTransactionRequest` still needs the matching `declineCode` field added to complete the chain.

## Related

- [[Capture an unknown-named JSON field with Jackson @JsonAnySetter]]

%% ai-graph-start %%

**Related notes:**
- [[LUZ-157476 decline taxonomy maps codes at luz_online_payment boundary]]
- [[Payrexx card declines reach luz_store as ERROR with prose, not DECLINED]]
- [[Payrexx v1.0 charge API returns only status+message on failure — no ISO 8583 code]]
- [[luz_online_payment silently drops Payrexx decline codes]]
- [[LUZ-157476 maps failure categories in luz_store only, overriding the boundary recommendation]]

**Relations:**
- LUZ-157476 — *describes* — decline-code flow
- decline-code flow — *involves* — luz-online-payment
- decline-code flow — *involves* — luz_store
- FailureCategory — *is keyed off* — ISO 8583 two-digit decline code
- ISO 8583 two-digit decline code — *from* — Payrexx
- message — *from* — Stripe
- message — *from* — Payrexx
- luz-online-payment — *extracts code from* — Transaction
- Transaction — *is a* — Payrexx response
- luz-online-payment — *forwards* — declineCode
- declineCode — *is a field on* — KlaraTransactionRequest
- luz-online-payment — *does not map to* — FailureCategory
- luz_store — *owns* — FailureCategory enum
- luz_store — *owns* — FailureCategory.map(...)
- FailureCategory.map(...) — *keys off* — declineCode
- FailureCategory.map(...) — *uses as fallback* — prose keyword-matching
- DECLINED transaction — *goes through* — success path
- success path — *uses* — KlaraTransactionRequestConverter
- wrapper-error — *goes through* — TransactionTask catch block
- Payrexx API — *has* — JSON field name
- JSON field name — *for* — declineCode
- Jackson @JsonAnySetter — *captures* — unknown-named JSON field
- KlaraTransactionRequest — *needs* — declineCode field
- LUZ-157476 — *related to* — Capture an unknown-named JSON field with Jackson @JsonAnySetter

%% ai-graph-end %%