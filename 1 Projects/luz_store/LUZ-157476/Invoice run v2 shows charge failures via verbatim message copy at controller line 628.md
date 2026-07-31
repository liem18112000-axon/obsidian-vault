---
title: "Invoice run v2 shows charge failures via verbatim message copy at controller line 628"
created: 2026-07-24
type: concept
status: budding
source: "LUZ-157476 session 2026-07-24"
tags: [luz-store, invoice-run, payrexx, message-mapping]
---

# Invoice run v2 shows charge failures via verbatim message copy at controller line 628

In luz_store invoice run v2, the UI Message column for failed charges is populated by ONE line: invoiceItem.setMessage(klaraTransactionRequestAfterCharge.getMessage()) (InvoiceRunServiceController.java:628, inside handleChargeCredit) — the in-flight charge-response message copied verbatim, no localization or mapping. The persisted InvoiceCreditCardTransaction.errorMessage is an audit copy that nothing reads back for display.

TransactionState.FAILED controls message survival, not content: isWarningState (controller:782) checks NOT_FINISHED transactions for FAILED/REFUND_FAILED/ROLLBACKED — if any, the item ends as PDF_CREATED_WARNING and keeps the message; otherwise the message is wiped (controller:674). The charging state itself goes CREDIT_CARD_CHARGED unconditionally even on failure (controller:638).

Consequences: DECLINED (message=null) → warning state with EMPTY message in UI. Line 628 is the single injection point for category→localized-string display in LUZ-157476 Phase 4. Also an NPE risk: handleChargeByCreditCard can return null (controller:853-856) but controller:627 dereferences without null check.

## Related
- [[1 Projects/luz_store/LUZ-157476/Payrexx card declines reach luz_store as ERROR with prose, not DECLINED]]
- [[DECLINED status falls through invoice charge-failure handling in luz_store]]
