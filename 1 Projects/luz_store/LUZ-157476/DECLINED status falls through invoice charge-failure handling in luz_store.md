---
title: "DECLINED status falls through invoice charge-failure handling in luz_store"
created: 2026-07-23
type: observation
status: seedling
source: "LUZ-157476 investigation 2026-07-23"
tags: [luz-store, payrexx, invoice-run, gotcha]
---

# DECLINED status falls through invoice charge-failure handling in luz_store

A Payrexx charge that comes back with status DECLINED is persisted (transactionState=FAILED, errorMessage stored) but the invoice itself is never marked as charge-failed. `InvoiceRunServiceController.handleChargeFailedOrRefundFailed` (line ~879) only branches on ERROR, missing-payment (payment==null && status==null), REFUND_FAILED, and TECHNICAL_ERROR — DECLINED matches none of them, so the method silently does nothing: no `chargedByCreditCardFail=true`, no customer-facing fail message, no exception.

The only reference to `TransactionStatus.DECLINED` in the whole luz_store codebase is the enum declaration itself. Confirmed relevance for LUZ-157476 (failure-reason taxonomy story): the AC requires routing the decline reason into email + in-app message, which is impossible for DECLINED while this path never fires. Fix candidate: include DECLINED in `isChargedFail` or give it its own branch.

Full investigation report: luz_store/docs/luz-157476/README.md

## Related
- [[luz_store TransactionStatus mirrors Payrexx API statuses plus two Klara-only values]]
