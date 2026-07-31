---
title: "Observed Payrexx prose vocabulary in dev is only three messages"
created: 2026-07-24
type: observation
status: budding
source: "dev AlloyDB export 2026-07-24"
tags: [luz-store, payrexx, taxonomy, data]
---

# Observed Payrexx prose vocabulary in dev is only three messages

Aggregated dev AlloyDB export of invoice_item.message (2026-07-24, luz_store docs/luz-157476/Invoice_Item_Messages_Payment.xlsx): the entire Payrexx failure-prose vocabulary observed in data is three messages — 'An error occurred: Charge of pre-authorization failed.' (192x), 'An error occurred: Your card has expired.' (160x), 'An error occurred: Your card was declined.' (1x). The LUZ-157476 five-category mapping covers all of them (expired→card-expired, declined→blocked-by-issuer, pre-auth-failed→other pending code lookup), so an Outcome-B prose mapper starts at 100% coverage of historical data.

Also found: one row with a leaked Java NPE text ('Cannot invoke ...KlaraTransactionRequest.get...') stored as a customer-visible invoice message — production evidence of the null-return dereference at InvoiceRunServiceController.java:627 (handleChargeByCreditCard can return null at :853-856).

## Related
- [[Invoice run v2 shows charge failures via verbatim message copy at controller line 628]]
- [[LUZ-157476 decline taxonomy maps codes at luz_online_payment boundary]]
