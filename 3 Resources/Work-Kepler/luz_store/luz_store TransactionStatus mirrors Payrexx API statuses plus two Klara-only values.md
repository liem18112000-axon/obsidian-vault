---
title: "luz_store TransactionStatus mirrors Payrexx API statuses plus two Klara-only values"
created: 2026-07-23
type: concept
status: seedling
source: "LUZ-157476 investigation 2026-07-23"
tags: [luz-store, payrexx, invoice-run, klarapay]
---

# luz_store TransactionStatus mirrors Payrexx API statuses plus two Klara-only values

luz_store's `TransactionStatus` enum (onlinepayment/model) has 14 values: 12 mirror the Payrexx API transaction statuses verbatim (waiting, confirmed, cancelled, declined, authorized, reserved, refunded, refund_pending, partially-refunded, chargeback, error, uncaptured) and 2 are Klara-only additions — TECHNICAL_ERROR (`technical-error`, set by CreditCardTransactionService when the REST call to the KlaraPay online-payment service throws) and REFUND_FAILED (`refund-failed`).

The only decline detail luz_store persists is the free-text `errorMessage`, mapped from `KlaraTransactionRequest.message` in `InvoiceCreditCardTransactionConverter`. Raw ISO 8583 decline codes never reach luz_store — they stay in Payrexx/KlaraPay (the Payrexx integration lives in the separate online-payment service, config key LUZ_ONLINE_PAYMENT_KEY, not in any locally cloned Kepler repo).

State derivation in the converter: CONFIRMED/REFUNDED/PARTIALLY_REFUNED → SUCCESS, REFUND_FAILED → REFUND_FAILED, everything else (incl. DECLINED) → FAILED.

## Related
- [[DECLINED status falls through invoice charge-failure handling in luz_store]]
- [[Payrexx ISO 8583 decline code to meaning reference table]]
