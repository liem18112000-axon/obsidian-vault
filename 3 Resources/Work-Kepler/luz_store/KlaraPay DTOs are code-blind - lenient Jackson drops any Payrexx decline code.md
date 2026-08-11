---
ai_hash: 809d7ba99a7303ee
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-23
entities: []
source: luz_online_payment code verification 2026-07-23
status: budding
tags:
- klarapay
- payrexx
- jackson
- deserialization
- gotcha
title: KlaraPay DTOs are code-blind - lenient Jackson drops any Payrexx decline code
type: lesson
---

# KlaraPay DTOs are code-blind - lenient Jackson drops any Payrexx decline code

luz_online_payment (KlaraPay) captures no structured Payrexx decline code: the consumer Transaction DTO parses only id/amount/referenceId/uuid/time/status/lang/psp/pspId/payrexx_fee/preAuthorizationId/payment/metadata/invoice/contact (Transaction.java:17-81) and PayrexxResponse only status/message/data — no code/errorCode/reason field exists. Because the ObjectMapper sets FAIL_ON_UNKNOWN_PROPERTIES=false (ObjectMapperFactory.java:29,36), any ISO 8583 code Payrexx might include is silently discarded during deserialization; the webhook path uses the same code-blind model.

Lesson: lenient deserialization (ignore-unknown) hides available upstream data — when hunting for 'does the provider send X', reading the DTO tells you what is CONSUMED, never what is SENT. Wire-capture is required for the difference. Payrexx is called via its Transaction API (PayrexxTransactionRestClient, POST Transaction/{id}), base URL https://api.klarapay.ch/ (whitelabel domain).

## Related
- [[1 Projects/luz_store/LUZ-157476/Payrexx card declines reach luz_store as ERROR with prose, not DECLINED]]
- [[Payrexx ISO 8583 decline code to meaning reference table]]

%% ai-graph-start %%

**Related notes:**
- [[Enumerate real Payrexx decline codes via chargeTransactionId lookup, not via service responses]]
- [[luz_online_payment silently drops Payrexx decline codes]]
- [[Payrexx v1.0 charge API returns only status+message on failure — no ISO 8583 code]]
- [[KlaraPay V2 Java classes still call Payrexx API v1.0 on the consumer flow]]
- [[luz_online_payment notify webhook silently 400-rejects ~43% of Payrexx webhooks on dev]]

%% ai-graph-end %%