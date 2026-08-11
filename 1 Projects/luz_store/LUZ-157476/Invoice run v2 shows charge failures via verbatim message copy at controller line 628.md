---
ai_hash: e7ee6809b0619548
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-24
entities:
- Invoice run v2
- charge failures
- verbatim message copy
- line 628
- luz_store
- UI Message column
- invoiceItem
- klaraTransactionRequestAfterCharge
- InvoiceRunServiceController.java
- handleChargeCredit
- InvoiceCreditCardTransaction.errorMessage
- TransactionState.FAILED
- isWarningState
- line 782
- NOT_FINISHED transactions
- FAILED
- REFUND_FAILED
- ROLLBACKED
- PDF_CREATED_WARNING
- line 674
- CREDIT_CARD_CHARGED
- line 638
- DECLINED
- LUZ-157476 Phase 4
- NPE risk
- handleChargeByCreditCard
- lines 853-856
- line 627
- Payrexx card declines
- ERROR
- prose
- invoice charge-failure handling
- warning state
- message survival
- category→localized-string display
- null check
- invoiceItem.setMessage(klaraTransactionRequestAfterCharge.getMessage())
source: LUZ-157476 session 2026-07-24
status: budding
tags:
- luz-store
- invoice-run
- payrexx
- message-mapping
title: Invoice run v2 shows charge failures via verbatim message copy at controller
  line 628
type: concept
---

# Invoice run v2 shows charge failures via verbatim message copy at controller line 628

In luz_store invoice run v2, the UI Message column for failed charges is populated by ONE line: invoiceItem.setMessage(klaraTransactionRequestAfterCharge.getMessage()) (InvoiceRunServiceController.java:628, inside handleChargeCredit) — the in-flight charge-response message copied verbatim, no localization or mapping. The persisted InvoiceCreditCardTransaction.errorMessage is an audit copy that nothing reads back for display.

TransactionState.FAILED controls message survival, not content: isWarningState (controller:782) checks NOT_FINISHED transactions for FAILED/REFUND_FAILED/ROLLBACKED — if any, the item ends as PDF_CREATED_WARNING and keeps the message; otherwise the message is wiped (controller:674). The charging state itself goes CREDIT_CARD_CHARGED unconditionally even on failure (controller:638).

Consequences: DECLINED (message=null) → warning state with EMPTY message in UI. Line 628 is the single injection point for category→localized-string display in LUZ-157476 Phase 4. Also an NPE risk: handleChargeByCreditCard can return null (controller:853-856) but controller:627 dereferences without null check.

## Related
- [[1 Projects/luz_store/LUZ-157476/Payrexx card declines reach luz_store as ERROR with prose, not DECLINED]]
- [[DECLINED status falls through invoice charge-failure handling in luz_store]]

%% ai-graph-start %%

**Related notes:**
- [[Observed Payrexx prose vocabulary in dev is only three messages]]
- [[Payrexx card declines reach luz_store as ERROR with prose, not DECLINED]]
- [[DECLINED status falls through invoice charge-failure handling in luz_store]]
- [[KlaraTransactionRequest.message content is Payrexx prose or runtime exception text, never a mapped constant]]
- [[TECHNICAL_ERROR is not retried in-flight but is retry-eligible on invoice-item rerun]]

**Relations:**
- Invoice run v2 — *shows* — charge failures
- charge failures — *are via* — verbatim message copy
- verbatim message copy — *occurs at* — line 628
- luz_store — *uses* — Invoice run v2
- UI Message column — *for* — failed charges
- UI Message column — *is populated by* — invoiceItem.setMessage(klaraTransactionRequestAfterCharge.getMessage())
- invoiceItem.setMessage(klaraTransactionRequestAfterCharge.getMessage()) — *is located at* — InvoiceRunServiceController.java:628
- InvoiceRunServiceController.java:628 — *is within* — handleChargeCredit
- InvoiceCreditCardTransaction.errorMessage — *is an* — audit copy
- InvoiceCreditCardTransaction.errorMessage — *is not read for* — display
- TransactionState.FAILED — *controls* — message survival
- isWarningState — *is at* — line 782
- isWarningState — *checks* — NOT_FINISHED transactions
- NOT_FINISHED transactions — *can be* — FAILED
- NOT_FINISHED transactions — *can be* — REFUND_FAILED
- NOT_FINISHED transactions — *can be* — ROLLBACKED
- item — *ends as* — PDF_CREATED_WARNING
- PDF_CREATED_WARNING — *keeps* — message
- message — *is wiped by* — line 674
- charging state — *goes* — CREDIT_CARD_CHARGED
- CREDIT_CARD_CHARGED — *occurs at* — line 638
- CREDIT_CARD_CHARGED — *occurs even on* — failure
- DECLINED — *leads to* — warning state
- warning state — *has* — EMPTY message
- line 628 — *is a* — single injection point
- single injection point — *for* — category→localized-string display
- line 628 — *is relevant for* — LUZ-157476 Phase 4
- NPE risk — *caused by* — handleChargeByCreditCard
- handleChargeByCreditCard — *can return* — null
- null — *at* — lines 853-856
- line 627 — *performs* — dereference without null check
- Payrexx card declines — *reach* — luz_store
- Payrexx card declines — *as* — ERROR
- Payrexx card declines — *contain* — prose
- Payrexx card declines — *are not* — DECLINED
- DECLINED — *falls through* — invoice charge-failure handling
- invoice charge-failure handling — *is in* — luz_store

%% ai-graph-end %%