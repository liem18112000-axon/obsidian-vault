---
title: "Enumerate real Payrexx decline codes via chargeTransactionId lookup, not via service responses"
created: 2026-07-24
type: howto
status: seedling
source: "LUZ-157476 session 2026-07-24"
tags: [payrexx, klarapay, luz-store, debugging]
---

# Enumerate real Payrexx decline codes via chargeTransactionId lookup, not via service responses

The KlaraTransactionRequest response from luz_online_payment structurally cannot reveal Payrexx decline codes (no code field), and the deserialized Payrexx response drops unknown fields. To enumerate codes that actually occur: (1) zero-code path — take chargeTransactionId values from failed INVOICE_CREDIT_CARD_TRANSACTION rows (errorMessage non-null) and look those transactions up in the Payrexx/KlaraPay merchant backoffice, which displays the ISO 8583 decline code per transaction; (2) no-deploy path — GET https://api.klarapay.ch/v1.0/Transaction/{id} directly with the service's instance/apiKey credentials and inspect raw JSON to learn whether the v1.0 API carries a code field at all; (3) deploy path — ClientResponseFilter on PayrexxTransactionRestClient logging the raw body pre-Jackson, plus the webhook resource.

Expectation: Payrexx public docs show no decline-code field on the API transaction object — codes likely live only in the backoffice UI, so the API mapping input probably stays prose.

## Related
- [[KlaraPay DTOs are code-blind - lenient Jackson drops any Payrexx decline code]]
- [[3 Resources/Work-Kepler/Payrexx/Payrexx ISO 8583 decline code to meaning reference table]]
