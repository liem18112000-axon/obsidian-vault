---
title: "A read timeout on a payment charge is ambiguous — reconcile before retrying"
created: 2026-08-03
type: lesson
status: seedling
source: "PROD investigation 2026-08-03 (item 256195)"
tags: [payments, idempotency, timeout, double-charge, gotcha]
---

# A read timeout on a payment charge is ambiguous — reconcile before retrying

A **read/socket timeout on a payment charge is not a decline** — it means the request reached the gateway but the response was lost. The charge may well have **succeeded** on the gateway even though the caller recorded it as FAILED/technical-error. Therefore **retrying blindly can double-charge** the customer.

## Rule
Before re-charging a timed-out transaction, **reconcile** against the gateway using the already-assigned transaction/reference id: look it up (Payrexx / KLARA pay); if it settled, mark the invoice paid; only re-charge if the gateway shows no successful charge.

## Where it bit us (LUZ, PROD 2026-08-03)
Invoice item 256195 (individual, 9.90 CHF): `luz-online-payment` → Payrexx charge (`TransactionRestCallerV2.chargeTransaction`) threw `SocketTimeoutException: Read timed out` for tx 38569922. luz-store `CreditCardTransactionService.chargeTransactions` **swallows** the exception → sets `TECHNICAL_ERROR` (the marker that triggers a re-charge on the next invoice run). No reconciliation of tx 38569922 was logged, so the true Payrexx outcome was unknown — a retry without reconciliation risked billing twice.

## Related
[[luz-store credit-card charge flow]] [[Re-wrapping a 5xx as 4xx defeats status-based retry]]

## Related

- [[Re-wrapping a 5xx as 4xx defeats status-based retry]]
