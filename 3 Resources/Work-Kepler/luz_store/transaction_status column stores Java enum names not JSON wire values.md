---
title: "transaction_status column stores Java enum names not JSON wire values"
created: 2026-07-24
type: lesson
status: seedling
source: "LUZ-157476 session 2026-07-24"
tags: [luz-store, jpa, enum, gotcha, sql]
---

# transaction_status column stores Java enum names not JSON wire values

In luz_store, invoice_credit_card_transaction.transaction_status is persisted with JPA @Enumerated(EnumType.STRING) (InvoiceCreditCardTransactionEntity.java:41-43), which stores the Java constant NAME — while the same TransactionStatus enum serializes to JSON via @JsonValue as lowercase wire values. So DB has DECLINED / TECHNICAL_ERROR / PARTIALLY_REFUNED / CHARGE_BACK / REFUND_PENDING, but the wire has declined / technical-error / partially-refunded / chargeback / refund_pending. SQL filters must use the uppercase constant names.

Trap: the PARTIALLY_REFUNED typo is locked into DB data — with EnumType.STRING, renaming the constant breaks JPA reads of all existing rows; fixing the spelling requires a data migration. Same rule for transaction_state and final_state columns.

## Related
- [[luz_store TransactionStatus mirrors Payrexx API statuses plus two Klara-only values]]
