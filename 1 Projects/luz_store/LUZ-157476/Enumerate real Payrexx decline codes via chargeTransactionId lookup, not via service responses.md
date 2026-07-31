---
ai_hash: 1a23e6f21e39ac7b
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-24
entities:
- Payrexx
- KlaraTransactionRequest
- luz_online_payment
- chargeTransactionId
- INVOICE_CREDIT_CARD_TRANSACTION
- Payrexx/KlaraPay merchant backoffice
- ISO 8583 decline code
- KlaraPay API v1.0
- ClientResponseFilter
- PayrexxTransactionRestClient
- Jackson
- Webhook resource
- Payrexx public documentation
- API transaction object
- KlaraPay DTOs
- Work-Kepler
- Payrexx ISO 8583 decline code to meaning reference table
source: LUZ-157476 session 2026-07-24
status: seedling
tags:
- payrexx
- klarapay
- luz-store
- debugging
title: Enumerate real Payrexx decline codes via chargeTransactionId lookup, not via
  service responses
type: howto
---

# Enumerate real Payrexx decline codes via chargeTransactionId lookup, not via service responses

The KlaraTransactionRequest response from luz_online_payment structurally cannot reveal Payrexx decline codes (no code field), and the deserialized Payrexx response drops unknown fields. To enumerate codes that actually occur: (1) zero-code path — take chargeTransactionId values from failed INVOICE_CREDIT_CARD_TRANSACTION rows (errorMessage non-null) and look those transactions up in the Payrexx/KlaraPay merchant backoffice, which displays the ISO 8583 decline code per transaction; (2) no-deploy path — GET https://api.klarapay.ch/v1.0/Transaction/{id} directly with the service's instance/apiKey credentials and inspect raw JSON to learn whether the v1.0 API carries a code field at all; (3) deploy path — ClientResponseFilter on PayrexxTransactionRestClient logging the raw body pre-Jackson, plus the webhook resource.

Expectation: Payrexx public docs show no decline-code field on the API transaction object — codes likely live only in the backoffice UI, so the API mapping input probably stays prose.

## Related
- [[KlaraPay DTOs are code-blind - lenient Jackson drops any Payrexx decline code]]
- [[3 Resources/Work-Kepler/Payrexx/Payrexx ISO 8583 decline code to meaning reference table]]

%% ai-graph-start %%

**Related notes:**
- [[KlaraPay DTOs are code-blind - lenient Jackson drops any Payrexx decline code]]
- [[Payrexx v1.0 charge API returns only status+message on failure — no ISO 8583 code]]
- [[luz_online_payment silently drops Payrexx decline codes]]
- [[KlaraPay V2 Java classes still call Payrexx API v1.0 on the consumer flow]]
- [[LUZ-157476 decline-code flow luz-online-payment forwards, luz_store maps]]

**Relations:**
- KlaraTransactionRequest — *originates from* — luz_online_payment
- KlaraTransactionRequest — *cannot reveal* — Payrexx decline codes
- Payrexx response — *drops* — unknown fields
- chargeTransactionId — *found in* — INVOICE_CREDIT_CARD_TRANSACTION
- Payrexx/KlaraPay merchant backoffice — *displays* — ISO 8583 decline code
- KlaraPay API v1.0 — *accessed via* — instance/apiKey credentials
- KlaraPay API v1.0 — *may contain* — code field
- ClientResponseFilter — *applied to* — PayrexxTransactionRestClient
- ClientResponseFilter — *logs* — raw body
- Payrexx public documentation — *does not show* — decline-code field
- ISO 8583 decline code — *visible in* — Payrexx/KlaraPay merchant backoffice
- KlaraPay DTOs — *are* — code-blind
- Jackson — *drops* — Payrexx decline code
- Payrexx — *has* — ISO 8583 decline code to meaning reference table
- Work-Kepler — *contains* — Payrexx ISO 8583 decline code to meaning reference table
- Payrexx — *is associated with* — KlaraPay
- API transaction object — *is part of* — Payrexx API

%% ai-graph-end %%