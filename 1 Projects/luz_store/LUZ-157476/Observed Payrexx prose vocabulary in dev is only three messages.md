---
ai_hash: 2816ad0414d76b09
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-24
entities:
- Payrexx
- dev
- invoice_item.message
- AlloyDB export
- '''An error occurred: Charge of pre-authorization failed.'''
- '''An error occurred: Your card has expired.'''
- '''An error occurred: Your card was declined.'''
- LUZ-157476
- five-category mapping
- expired (mapping category)
- card-expired (mapping target)
- declined (mapping category)
- blocked-by-issuer (mapping target)
- pre-auth-failed (mapping category)
- other pending code lookup (mapping target)
- Outcome-B prose mapper
- Java NPE text
- customer-visible invoice message
- null-return dereference
- InvoiceRunServiceController.java:627
- handleChargeByCreditCard
- InvoiceRunServiceController.java:853-856
- Invoice run v2
- charge failures
- controller line 628
- luz_online_payment boundary
- KlaraTransactionRequest.get
- luz_store docs/luz-157476/Invoice_Item_Messages_Payment.xlsx
source: dev AlloyDB export 2026-07-24
status: budding
tags:
- luz-store
- payrexx
- taxonomy
- data
title: Observed Payrexx prose vocabulary in dev is only three messages
type: observation
---

# Observed Payrexx prose vocabulary in dev is only three messages

Aggregated dev AlloyDB export of invoice_item.message (2026-07-24, luz_store docs/luz-157476/Invoice_Item_Messages_Payment.xlsx): the entire Payrexx failure-prose vocabulary observed in data is three messages — 'An error occurred: Charge of pre-authorization failed.' (192x), 'An error occurred: Your card has expired.' (160x), 'An error occurred: Your card was declined.' (1x). The LUZ-157476 five-category mapping covers all of them (expired→card-expired, declined→blocked-by-issuer, pre-auth-failed→other pending code lookup), so an Outcome-B prose mapper starts at 100% coverage of historical data.

Also found: one row with a leaked Java NPE text ('Cannot invoke ...KlaraTransactionRequest.get...') stored as a customer-visible invoice message — production evidence of the null-return dereference at InvoiceRunServiceController.java:627 (handleChargeByCreditCard can return null at :853-856).

## Related
- [[Invoice run v2 shows charge failures via verbatim message copy at controller line 628]]
- [[LUZ-157476 decline taxonomy maps codes at luz_online_payment boundary]]

%% ai-graph-start %%

**Related notes:**
- [[Invoice run v2 shows charge failures via verbatim message copy at controller line 628]]
- [[Payrexx card declines reach luz_store as ERROR with prose, not DECLINED]]
- [[LUZ-157476 decline taxonomy maps codes at luz_online_payment boundary]]
- [[KlaraTransactionRequest.message content is Payrexx prose or runtime exception text, never a mapped constant]]
- [[PROD shows Stripe under Payrexx and multilingual decline prose]]

**Relations:**
- Payrexx — *HAS_VOCABULARY_IN* — dev
- dev — *OBSERVED_MESSAGE* — 'An error occurred: Charge of pre-authorization failed.'
- dev — *OBSERVED_MESSAGE* — 'An error occurred: Your card has expired.'
- dev — *OBSERVED_MESSAGE* — 'An error occurred: Your card was declined.'
- AlloyDB export — *CONTAINS* — invoice_item.message
- AlloyDB export — *IS_FROM* — dev
- invoice_item.message — *IS_DOCUMENTED_IN* — luz_store docs/luz-157476/Invoice_Item_Messages_Payment.xlsx
- LUZ-157476 — *DEFINES* — five-category mapping
- five-category mapping — *COVERS* — 'An error occurred: Charge of pre-authorization failed.'
- five-category mapping — *COVERS* — 'An error occurred: Your card has expired.'
- five-category mapping — *COVERS* — 'An error occurred: Your card was declined.'
- five-category mapping — *MAPS* — expired (mapping category)
- expired (mapping category) — *TO* — card-expired (mapping target)
- five-category mapping — *MAPS* — declined (mapping category)
- declined (mapping category) — *TO* — blocked-by-issuer (mapping target)
- five-category mapping — *MAPS* — pre-auth-failed (mapping category)
- pre-auth-failed (mapping category) — *TO* — other pending code lookup (mapping target)
- Outcome-B prose mapper — *HAS_COVERAGE* — 100%
- Java NPE text — *IS_STORED_AS* — customer-visible invoice message
- Java NPE text — *IS_EVIDENCE_OF* — null-return dereference
- null-return dereference — *OCCURS_AT* — InvoiceRunServiceController.java:627
- handleChargeByCreditCard — *CAN_RETURN* — null
- handleChargeByCreditCard — *RETURNS_NULL_AT* — InvoiceRunServiceController.java:853-856
- Java NPE text — *MENTIONS* — KlaraTransactionRequest.get
- Invoice run v2 — *SHOWS* — charge failures
- charge failures — *ARE_VIA* — verbatim message copy
- verbatim message copy — *IS_AT* — controller line 628
- LUZ-157476 — *MAPS_CODES_AT* — luz_online_payment boundary

%% ai-graph-end %%