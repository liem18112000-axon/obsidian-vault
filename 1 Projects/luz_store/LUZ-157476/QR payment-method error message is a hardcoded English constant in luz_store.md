---
ai_hash: c6c668eb4a4249a5
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-23
entities:
- QR payment-method error message
- luz_store
- InvoiceRunMessageConstants.WRONG_PAYMENT_METHOD_FOR_INDIVIDUAL_TENANT_ERROR_MESSAGE
- utils/InvoiceRunMessageConstants.java
- InvoiceRunServiceController.handleWrongPaymentMethodForIndividualTenant
- controller:550-558
- CREDIT_CARD_CHARGED_PENDING
- pre-charge validation failure
- card decline
- LUZ-157476
- decline taxonomy
- localized display mechanism
- customer-friendly-messages AC
- InvoiceRunMessageConstants
- luz_online_payment
source: LUZ-157476 open-questions analysis 2026-07-23
status: seedling
tags:
- luz-store
- invoice-run
- i18n
- gotcha
title: QR payment-method error message is a hardcoded English constant in luz_store
type: observation
---

# QR payment-method error message is a hardcoded English constant in luz_store

The invoice-run UI message 'QR Payment method is not allowed for individual tenant' is owned by luz_store — a hardcoded English constant `InvoiceRunMessageConstants.WRONG_PAYMENT_METHOD_FOR_INDIVIDUAL_TENANT_ERROR_MESSAGE` (utils/InvoiceRunMessageConstants.java:13), set on the invoice item in `InvoiceRunServiceController.handleWrongPaymentMethodForIndividualTenant` (controller:550-558) along with state CREDIT_CARD_CHARGED_PENDING.

It is a pre-charge validation failure, not a card decline — recommendation for LUZ-157476: keep it OUT of the decline taxonomy (would pollute decline metrics) but route it through the same localized display mechanism, since a hardcoded English string in the same UI column undermines the customer-friendly-messages AC. All other constants in that class are hardcoded English too.

## Related
- [[LUZ-157476 decline taxonomy maps codes at luz_online_payment boundary]]

%% ai-graph-start %%

**Related notes:**
- [[Invoice run v2 shows charge failures via verbatim message copy at controller line 628]]
- [[Write-time localization into the existing message column avoids schema change]]
- [[Observed Payrexx prose vocabulary in dev is only three messages]]
- [[DECLINED status falls through invoice charge-failure handling in luz_store]]
- [[LUZ-157476 decline taxonomy maps codes at luz_online_payment boundary]]

**Relations:**
- QR payment-method error message — *is a hardcoded English constant in* — luz_store
- QR payment-method error message — *is identified as* — InvoiceRunMessageConstants.WRONG_PAYMENT_METHOD_FOR_INDIVIDUAL_TENANT_ERROR_MESSAGE
- InvoiceRunMessageConstants.WRONG_PAYMENT_METHOD_FOR_INDIVIDUAL_TENANT_ERROR_MESSAGE — *is located in* — utils/InvoiceRunMessageConstants.java
- InvoiceRunMessageConstants.WRONG_PAYMENT_METHOD_FOR_INDIVIDUAL_TENANT_ERROR_MESSAGE — *is set by* — InvoiceRunServiceController.handleWrongPaymentMethodForIndividualTenant
- InvoiceRunServiceController.handleWrongPaymentMethodForIndividualTenant — *is located at* — controller:550-558
- InvoiceRunServiceController.handleWrongPaymentMethodForIndividualTenant — *sets state* — CREDIT_CARD_CHARGED_PENDING
- QR payment-method error message — *is a* — pre-charge validation failure
- QR payment-method error message — *is not a* — card decline
- LUZ-157476 — *is related to* — decline taxonomy
- QR payment-method error message — *should use* — localized display mechanism
- hardcoded English constant — *undermines* — customer-friendly-messages AC
- InvoiceRunMessageConstants — *contains* — hardcoded English constants
- LUZ-157476 — *maps codes at boundary* — luz_online_payment

%% ai-graph-end %%