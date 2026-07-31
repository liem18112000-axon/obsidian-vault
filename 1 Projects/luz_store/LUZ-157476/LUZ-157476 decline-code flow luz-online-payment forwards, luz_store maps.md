---
title: "LUZ-157476 decline-code flow: luz-online-payment forwards, luz_store maps"
created: 2026-07-29
type: decision
status: seedling
source: "session 2026-07-29; luz_store/docs/luz-157476/PAYREXX-RESPONSE.md"
tags: [luz-157476, payrexx, klarapay, failure-category, online-payment]
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
