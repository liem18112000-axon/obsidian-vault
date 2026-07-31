---
title: "KlaraPay V2 Java classes still call Payrexx API v1.0 on the consumer flow"
created: 2026-07-24
type: lesson
status: budding
source: "luz_online_payment re-verification 2026-07-24"
tags: [klarapay, payrexx, api-versioning, gotcha]
---

# KlaraPay V2 Java classes still call Payrexx API v1.0 on the consumer flow

In luz_online_payment, the 'V2' suffix on PayrexxCommunicatorV2 / TransactionRestCallerV2 is an internal Java refactor generation — NOT the Payrexx API version. The consumer charge flow's REST client URL is ${payrexx.base.domain.url}${payrexx.consumer.api.version} with PAYREXX.CONSUMER.API.VERSION='v1.0' (microprofile-config.properties:23, docker-compose.yaml:36-41), so charges hit .../v1.0/Transaction/{id}. Merchant flows use v2.0. There is only one shared TransactionStatus enum in the codebase.

Two consequences: (1) status/decline documentation from developers.payrexx.com describes the CURRENT API — the v1.0 wire vocabulary must be validated by payload capture before trusting it; (2) the same upstream HTTP failure produces different message text depending on Java path — V1 callers use an interceptor wrapping raw response bodies into RuntimeException, the V2 charge path uses RestClientExceptionMapper ('KLARA Pay server is unavailable!', 422→'The instance is inactive').

Lesson: never infer API version from class-name suffixes — read the resolved base URL config.

## Related
- [[KlaraPay DTOs are code-blind - lenient Jackson drops any Payrexx decline code]]
- [[Prod card declines reach luz_store as ERROR plus Payrexx prose, not DECLINED]]
