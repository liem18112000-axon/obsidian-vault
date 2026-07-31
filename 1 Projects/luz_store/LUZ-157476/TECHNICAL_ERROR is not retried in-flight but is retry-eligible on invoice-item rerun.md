---
title: "TECHNICAL_ERROR is not retried in-flight but is retry-eligible on invoice-item rerun"
created: 2026-07-24
type: concept
status: budding
source: "LUZ-157476 session 2026-07-24"
tags: [luz-store, invoice-run, retry, payrexx]
---

# TECHNICAL_ERROR is not retried in-flight but is retry-eligible on invoice-item rerun

luz_store retries TECHNICAL_ERROR charges at exactly one level. In-flight: none — CreditCardTransactionService imports MicroProfile @Retry but never applies it, so one failed REST call marks the whole batch TECHNICAL_ERROR. On rerun: yes — handleChargeCredit (InvoiceRunServiceController:622-623) re-charges when isTransactionToBeRetriedMarkedAsTechnicalError (:649-653, LUZ-92848, last transaction of the same invoiceRunUuid has status TECHNICAL_ERROR) or shouldProcessChargeTransactions (:777-783, any prior NOT_FINISHED transaction with state FAILED/REFUND_FAILED/ROLLBACKED).

Double-charge protection: TECHNICAL_ERROR can hide an upstream success (timeout after Payrexx charged), so before re-charging, getPayrexxTransactions (:859) searches Payrexx by referenceId=invoiceItem.id within startChargingDate+5min and feeds completedTransactions into the charge call.

Gotcha for LUZ-157476: DECLINED also persists as state FAILED, making hard declines equally retry-eligible — retrying a stolen-card decline is pointless-to-harmful; category-aware retry policy (technical→retry, expired/blocked→re-register card) is the fix.

## Related
- [[Fault-tolerance annotations imported but never applied in CreditCardTransactionService]]
- [[DECLINED status falls through invoice charge-failure handling in luz_store]]
