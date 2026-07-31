---
title: "QR payment-method error message is a hardcoded English constant in luz_store"
created: 2026-07-23
type: observation
status: seedling
source: "LUZ-157476 open-questions analysis 2026-07-23"
tags: [luz-store, invoice-run, i18n, gotcha]
---

# QR payment-method error message is a hardcoded English constant in luz_store

The invoice-run UI message 'QR Payment method is not allowed for individual tenant' is owned by luz_store — a hardcoded English constant `InvoiceRunMessageConstants.WRONG_PAYMENT_METHOD_FOR_INDIVIDUAL_TENANT_ERROR_MESSAGE` (utils/InvoiceRunMessageConstants.java:13), set on the invoice item in `InvoiceRunServiceController.handleWrongPaymentMethodForIndividualTenant` (controller:550-558) along with state CREDIT_CARD_CHARGED_PENDING.

It is a pre-charge validation failure, not a card decline — recommendation for LUZ-157476: keep it OUT of the decline taxonomy (would pollute decline metrics) but route it through the same localized display mechanism, since a hardcoded English string in the same UI column undermines the customer-friendly-messages AC. All other constants in that class are hardcoded English too.

## Related
- [[LUZ-157476 decline taxonomy maps codes at luz_online_payment boundary]]
