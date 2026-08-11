---
ai_hash: c5840585d94e46a6
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-23
entities:
- DECLINED status
- invoice charge-failure handling
- luz_store
- Payrexx charge
- transactionState
- errorMessage
- InvoiceRunServiceController.handleChargeFailedOrRefundFailed
- ERROR
- missing-payment
- REFUND_FAILED
- TECHNICAL_ERROR
- chargedByCreditCardFail
- customer-facing fail message
- exception
- TransactionStatus.DECLINED
- LUZ-157476
- failure-reason taxonomy story
- AC
- email
- in-app message
- isChargedFail
- luz_store/docs/luz-157476/README.md
- luz_store TransactionStatus
- Payrexx API statuses
- Klara-only values
source: LUZ-157476 investigation 2026-07-23
status: seedling
tags:
- luz-store
- payrexx
- invoice-run
- gotcha
title: DECLINED status falls through invoice charge-failure handling in luz_store
type: observation
---

# DECLINED status falls through invoice charge-failure handling in luz_store

A Payrexx charge that comes back with status DECLINED is persisted (transactionState=FAILED, errorMessage stored) but the invoice itself is never marked as charge-failed. `InvoiceRunServiceController.handleChargeFailedOrRefundFailed` (line ~879) only branches on ERROR, missing-payment (payment==null && status==null), REFUND_FAILED, and TECHNICAL_ERROR — DECLINED matches none of them, so the method silently does nothing: no `chargedByCreditCardFail=true`, no customer-facing fail message, no exception.

The only reference to `TransactionStatus.DECLINED` in the whole luz_store codebase is the enum declaration itself. Confirmed relevance for LUZ-157476 (failure-reason taxonomy story): the AC requires routing the decline reason into email + in-app message, which is impossible for DECLINED while this path never fires. Fix candidate: include DECLINED in `isChargedFail` or give it its own branch.

Full investigation report: luz_store/docs/luz-157476/README.md

## Related
- [[luz_store TransactionStatus mirrors Payrexx API statuses plus two Klara-only values]]

%% ai-graph-start %%

**Related notes:**
- [[Payrexx card declines reach luz_store as ERROR with prose, not DECLINED]]
- [[LUZ-157476 decline taxonomy maps codes at luz_online_payment boundary]]
- [[Invoice run v2 shows charge failures via verbatim message copy at controller line 628]]
- [[TransactionStatus.from() swallows unknown Payrexx statuses as null]]
- [[Payrexx declines travel in-band on HTTP 2xx in the luz charge flow]]

**Relations:**
- DECLINED status — *falls through* — invoice charge-failure handling
- DECLINED status — *occurs in* — luz_store
- Payrexx charge — *returns* — DECLINED status
- DECLINED status — *persists as* — transactionState=FAILED
- DECLINED status — *stores* — errorMessage
- InvoiceRunServiceController.handleChargeFailedOrRefundFailed — *handles* — ERROR
- InvoiceRunServiceController.handleChargeFailedOrRefundFailed — *handles* — missing-payment
- InvoiceRunServiceController.handleChargeFailedOrRefundFailed — *handles* — REFUND_FAILED
- InvoiceRunServiceController.handleChargeFailedOrRefundFailed — *handles* — TECHNICAL_ERROR
- DECLINED status — *is not handled by* — InvoiceRunServiceController.handleChargeFailedOrRefundFailed
- InvoiceRunServiceController.handleChargeFailedOrRefundFailed — *does not set* — chargedByCreditCardFail
- InvoiceRunServiceController.handleChargeFailedOrRefundFailed — *does not show* — customer-facing fail message
- InvoiceRunServiceController.handleChargeFailedOrRefundFailed — *does not raise* — exception
- TransactionStatus.DECLINED — *is an enum in* — luz_store codebase
- LUZ-157476 — *is a* — failure-reason taxonomy story
- AC — *requires routing* — decline reason to email
- AC — *requires routing* — decline reason to in-app message
- DECLINED status — *prevents* — routing decline reason
- Fix candidate — *includes* — DECLINED status in isChargedFail
- Fix candidate — *suggests* — own branch for DECLINED status
- luz_store/docs/luz-157476/README.md — *is a* — Full investigation report
- luz_store TransactionStatus — *mirrors* — Payrexx API statuses
- luz_store TransactionStatus — *includes* — Klara-only values

%% ai-graph-end %%