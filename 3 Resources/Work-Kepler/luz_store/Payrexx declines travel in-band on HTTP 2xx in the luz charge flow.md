---
ai_hash: 849b681c7e2ecb02
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-23
entities: []
source: LUZ-157476 investigation 2026-07-23
status: seedling
tags:
- luz-store
- payrexx
- http
- rest-client
- gotcha
title: Payrexx declines travel in-band on HTTP 2xx in the luz charge flow
type: lesson
---

# Payrexx declines travel in-band on HTTP 2xx in the luz charge flow

In the luz invoice-run charge flow, a Payrexx decline arrives as HTTP 2xx with status=declined inside the response body — the HTTP status code never signals a decline. HTTP 4xx/5xx/timeouts from luz_online_payment mean transport/technical failure only, and luz_store collapses them all to TECHNICAL_ERROR via a catch-all, keeping just the exception message prose; the upstream HTTP code is never persisted as data (CreditCardTransactionService.java:52-67).

Related masking gotcha: the read-side calls (customer-payment/all, payment-info/card, transaction search) re-throw ANY upstream failure as HTTP 400 from luz_store, hiding the real 401/404/500. And InvoiceRunResource.chargeInvoiceRuns returns 201 Created even when every transaction in the body failed in-band (OpenAPI annotation claims 200 — docs/code mismatch).

Implication for failure taxonomy: HTTP-level failures can only ever map to the 'other/technical' category; distinguishing 'provider down' from 'config broken' requires capturing WebApplicationException.getResponse().getStatus() instead of message text.

## Related
- [[luz_store TransactionStatus mirrors Payrexx API statuses plus two Klara-only values]]
- [[LUZ-157476 decline taxonomy maps codes at luz_online_payment boundary]]

%% ai-graph-start %%

**Related notes:**
- [[Payrexx card declines reach luz_store as ERROR with prose, not DECLINED]]
- [[luz_online_payment silently drops Payrexx decline codes]]
- [[DECLINED status falls through invoice charge-failure handling in luz_store]]
- [[LUZ-157476 decline taxonomy maps codes at luz_online_payment boundary]]
- [[KlaraPay V2 Java classes still call Payrexx API v1.0 on the consumer flow]]

%% ai-graph-end %%