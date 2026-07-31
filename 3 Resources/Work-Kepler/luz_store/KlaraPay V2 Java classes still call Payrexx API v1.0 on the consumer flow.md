---
ai_hash: 4b1aa7c3e01197f5
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-24
entities: []
source: luz_online_payment re-verification 2026-07-24
status: budding
tags:
- klarapay
- payrexx
- api-versioning
- gotcha
title: KlaraPay V2 Java classes still call Payrexx API v1.0 on the consumer flow
type: lesson
---

# KlaraPay V2 Java classes still call Payrexx API v1.0 on the consumer flow

In luz_online_payment, the 'V2' suffix on PayrexxCommunicatorV2 / TransactionRestCallerV2 is an internal Java refactor generation — NOT the Payrexx API version. The consumer charge flow's REST client URL is ${payrexx.base.domain.url}${payrexx.consumer.api.version} with PAYREXX.CONSUMER.API.VERSION='v1.0' (microprofile-config.properties:23, docker-compose.yaml:36-41), so charges hit .../v1.0/Transaction/{id}. Merchant flows use v2.0. There is only one shared TransactionStatus enum in the codebase.

Two consequences: (1) status/decline documentation from developers.payrexx.com describes the CURRENT API — the v1.0 wire vocabulary must be validated by payload capture before trusting it; (2) the same upstream HTTP failure produces different message text depending on Java path — V1 callers use an interceptor wrapping raw response bodies into RuntimeException, the V2 charge path uses RestClientExceptionMapper ('KLARA Pay server is unavailable!', 422→'The instance is inactive').

Lesson: never infer API version from class-name suffixes — read the resolved base URL config.

## Related
- [[KlaraPay DTOs are code-blind - lenient Jackson drops any Payrexx decline code]]
- [[1 Projects/luz_store/LUZ-157476/Payrexx card declines reach luz_store as ERROR with prose, not DECLINED]]

%% ai-graph-start %%

**Related notes:**
- [[KlaraPay DTOs are code-blind - lenient Jackson drops any Payrexx decline code]]
- [[KlaraTransactionRequest.message content is Payrexx prose or runtime exception text, never a mapped constant]]
- [[Payrexx card declines reach luz_store as ERROR with prose, not DECLINED]]
- [[Enumerate real Payrexx decline codes via chargeTransactionId lookup, not via service responses]]
- [[Payrexx declines travel in-band on HTTP 2xx in the luz charge flow]]

%% ai-graph-end %%