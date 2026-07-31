---
title: "KlaraPay DTOs are code-blind - lenient Jackson drops any Payrexx decline code"
created: 2026-07-23
type: lesson
status: budding
source: "luz_online_payment code verification 2026-07-23"
tags: [klarapay, payrexx, jackson, deserialization, gotcha]
---

# KlaraPay DTOs are code-blind - lenient Jackson drops any Payrexx decline code

luz_online_payment (KlaraPay) captures no structured Payrexx decline code: the consumer Transaction DTO parses only id/amount/referenceId/uuid/time/status/lang/psp/pspId/payrexx_fee/preAuthorizationId/payment/metadata/invoice/contact (Transaction.java:17-81) and PayrexxResponse only status/message/data — no code/errorCode/reason field exists. Because the ObjectMapper sets FAIL_ON_UNKNOWN_PROPERTIES=false (ObjectMapperFactory.java:29,36), any ISO 8583 code Payrexx might include is silently discarded during deserialization; the webhook path uses the same code-blind model.

Lesson: lenient deserialization (ignore-unknown) hides available upstream data — when hunting for 'does the provider send X', reading the DTO tells you what is CONSUMED, never what is SENT. Wire-capture is required for the difference. Payrexx is called via its Transaction API (PayrexxTransactionRestClient, POST Transaction/{id}), base URL https://api.klarapay.ch/ (whitelabel domain).

## Related
- [[Prod card declines reach luz_store as ERROR plus Payrexx prose, not DECLINED]]
- [[Payrexx decline codes are ISO 8583 issuer codes from the card-issuing bank]]
