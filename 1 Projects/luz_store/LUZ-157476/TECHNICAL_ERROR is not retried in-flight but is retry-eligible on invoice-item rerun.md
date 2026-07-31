---
ai_hash: 670d3fb8fe98b69a
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-24
entities:
- TECHNICAL_ERROR
- in-flight retry
- invoice-item rerun
- luz_store
- CreditCardTransactionService
- MicroProfile @Retry
- REST call
- batch
- handleChargeCredit
- isTransactionToBeRetriedMarkedAsTechnicalError
- LUZ-92848
- invoiceRunUuid
- shouldProcessChargeTransactions
- NOT_FINISHED transaction
- FAILED
- REFUND_FAILED
- ROLLBACKED
- upstream success
- Payrexx
- getPayrexxTransactions
- referenceId
- invoiceItem.id
- startChargingDate
- completedTransactions
- charge call
- DECLINED
- LUZ-157476
- stolen-card decline
- category-aware retry policy
- Fault-tolerance annotations
- invoice charge-failure handling
source: LUZ-157476 session 2026-07-24
status: budding
tags:
- luz-store
- invoice-run
- retry
- payrexx
title: TECHNICAL_ERROR is not retried in-flight but is retry-eligible on invoice-item
  rerun
type: concept
---

# TECHNICAL_ERROR is not retried in-flight but is retry-eligible on invoice-item rerun

luz_store retries TECHNICAL_ERROR charges at exactly one level. In-flight: none — CreditCardTransactionService imports MicroProfile @Retry but never applies it, so one failed REST call marks the whole batch TECHNICAL_ERROR. On rerun: yes — handleChargeCredit (InvoiceRunServiceController:622-623) re-charges when isTransactionToBeRetriedMarkedAsTechnicalError (:649-653, LUZ-92848, last transaction of the same invoiceRunUuid has status TECHNICAL_ERROR) or shouldProcessChargeTransactions (:777-783, any prior NOT_FINISHED transaction with state FAILED/REFUND_FAILED/ROLLBACKED).

Double-charge protection: TECHNICAL_ERROR can hide an upstream success (timeout after Payrexx charged), so before re-charging, getPayrexxTransactions (:859) searches Payrexx by referenceId=invoiceItem.id within startChargingDate+5min and feeds completedTransactions into the charge call.

Gotcha for LUZ-157476: DECLINED also persists as state FAILED, making hard declines equally retry-eligible — retrying a stolen-card decline is pointless-to-harmful; category-aware retry policy (technical→retry, expired/blocked→re-register card) is the fix.

## Related
- [[Fault-tolerance annotations imported but never applied in CreditCardTransactionService]]
- [[DECLINED status falls through invoice charge-failure handling in luz_store]]

%% ai-graph-start %%

**Related notes:**
- [[DECLINED status falls through invoice charge-failure handling in luz_store]]
- [[Payrexx card declines reach luz_store as ERROR with prose, not DECLINED]]
- [[Payrexx declines travel in-band on HTTP 2xx in the luz charge flow]]
- [[Invoice run v2 shows charge failures via verbatim message copy at controller line 628]]
- [[Fault-tolerance annotations imported but never applied in CreditCardTransactionService]]

**Relations:**
- TECHNICAL_ERROR — *is not retried* — in-flight retry
- TECHNICAL_ERROR — *is retry-eligible on* — invoice-item rerun
- luz_store — *retries* — TECHNICAL_ERROR
- CreditCardTransactionService — *imports* — MicroProfile @Retry
- CreditCardTransactionService — *does not apply* — MicroProfile @Retry
- REST call — *failure marks* — batch
- batch — *as status* — TECHNICAL_ERROR
- handleChargeCredit — *re-charges based on* — isTransactionToBeRetriedMarkedAsTechnicalError
- handleChargeCredit — *re-charges based on* — shouldProcessChargeTransactions
- isTransactionToBeRetriedMarkedAsTechnicalError — *checks for status* — TECHNICAL_ERROR
- isTransactionToBeRetriedMarkedAsTechnicalError — *involves* — invoiceRunUuid
- LUZ-92848 — *is related to* — isTransactionToBeRetriedMarkedAsTechnicalError
- shouldProcessChargeTransactions — *checks for* — NOT_FINISHED transaction
- NOT_FINISHED transaction — *has state* — FAILED
- NOT_FINISHED transaction — *has state* — REFUND_FAILED
- NOT_FINISHED transaction — *has state* — ROLLBACKED
- TECHNICAL_ERROR — *can hide* — upstream success
- getPayrexxTransactions — *searches* — Payrexx
- getPayrexxTransactions — *uses parameter* — referenceId
- referenceId — *is* — invoiceItem.id
- getPayrexxTransactions — *uses time window* — startChargingDate
- getPayrexxTransactions — *feeds* — completedTransactions
- completedTransactions — *into* — charge call
- DECLINED — *persists as state* — FAILED
- DECLINED — *is* — retry-eligible
- LUZ-157476 — *is related to* — DECLINED persists as state FAILED
- category-aware retry policy — *is a fix for* — stolen-card decline
- Fault-tolerance annotations — *are imported by* — CreditCardTransactionService
- Fault-tolerance annotations — *are not applied in* — CreditCardTransactionService
- DECLINED — *status falls through* — invoice charge-failure handling
- invoice charge-failure handling — *is in* — luz_store

%% ai-graph-end %%