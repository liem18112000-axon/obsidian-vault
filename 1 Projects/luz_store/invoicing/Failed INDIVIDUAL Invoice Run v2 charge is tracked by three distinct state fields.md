---
title: "Failed INDIVIDUAL Invoice Run v2 charge is tracked by three distinct state fields"
created: 2026-08-04
type: lesson
status: seedling
source: "session 2026-08-04"
tags: [luz_store, invoice-run-v2, charge-tracking, LUZ-157478, gotcha]
---

# Failed INDIVIDUAL Invoice Run v2 charge is tracked by three distinct state fields

A failed **INDIVIDUAL** step-2 credit-card charge in Invoice Run v2 is represented across **three distinct state fields** — different enums, different tables — and the one most people reach for (`transaction_state`) is the *least* reliable signal.

| Field | Table | Enum | Failure value |
|---|---|---|---|
| `state` | `invoice_item` | `InvoiceItemState` | **`CREDIT_CARD_CHARGED_PENDING`** (individual) / `CREDIT_CARD_CHARGED_FAILED` (company) |
| `state` | `invoice_charge_tracking` | `ChargeTrackingState` | **`PENDING`** (fresh) or **`SUSPENDED`** (older row superseded) |
| `transaction_state` | `invoice_credit_card_transaction` | `TransactionState` | `FAILED` — but the row is optional, and code matches on `transaction_status=ERROR` |

## Why

- The COMPANY vs INDIVIDUAL split is in `InvoiceRunServiceController` (~lines 562-568 and 668-672): on a failed CREDIT charge an individual item goes to `CREDIT_CARD_CHARGED_PENDING`, a company item to `CREDIT_CARD_CHARGED_FAILED`. So individuals essentially never show `ChargeTrackingState.FAILED` on a normal fail.
- `ChargeTrackingState.PENDING`/`SUSPENDED` and `TransactionState` are **different enums**. `transaction_state` can only ever be SUCCESS/FAILED/ROLLBACKED/REFUND_FAILED (`InvoiceCreditCardTransactionConverter`) — PENDING/SUSPENDED never appear there. Conflating them produces a wrong verify query.
- The `invoice_credit_card_transaction` row is written **only if Payrexx actually returned a decline**. A timeout/exception leaves the item PENDING with no transaction row at all (the notification trigger has a fallback for exactly this).

## Authoritative signal

The notification flow (`ChargeFailedNotificationTrigger.findFailedItems`) keys off **`invoice_item.state = CREDIT_CARD_CHARGED_PENDING`** + payment method CREDIT. That is the source of truth for "this individual charge failed and needs retry/notification" — not `transaction_state=FAILED`. The CC transaction, when present, is matched by `transaction_status=ERROR`, not by `transaction_state`.

Discovered while correcting the verify step in the `luz-store-seed-failed-charge` skill (LUZ-157478).

## Related

- [[Payrexx card declines reach luz_store as ERROR with prose]]
- [[not DECLINED]]
- [[DECLINED status falls through invoice charge-failure handling in luz_store]]
- [[Invoice run v2 shows charge failures via verbatim message copy at controller line 628]]
