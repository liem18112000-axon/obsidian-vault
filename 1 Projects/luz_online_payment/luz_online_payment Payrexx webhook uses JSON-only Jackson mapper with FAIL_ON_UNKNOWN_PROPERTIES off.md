---
ai_hash: a30c510224c9928c
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-31
entities:
- luz_online_payment
- Payrexx
- webhook
- Jackson mapper
- JSON
- FAIL_ON_UNKNOWN_PROPERTIES
- POST /transactions/notify
- PayrexxNotifyTransactionResource
- RESTEasy
- Jackson JAX-RS provider
- ObjectMapper
- RESTContextResolver
- ObjectMapperFactory
- Transaction
- '@JsonAnySetter'
- application/json
- application/x-www-form-urlencoded
- 415 Unsupported Media Type
- MerchantService.java:115
- metadata
- LinkedHashMap
- decline_code
- consumers
source: LUZ-157476 investigation 2026-07
status: seedling
tags:
- payrexx
- webhook
- resteasy
- jackson
- luz-online-payment
- luz-157476
title: luz_online_payment Payrexx webhook uses JSON-only Jackson mapper with FAIL_ON_UNKNOWN_PROPERTIES
  off
type: observation
---

# luz_online_payment Payrexx webhook uses JSON-only Jackson mapper with FAIL_ON_UNKNOWN_PROPERTIES off

The `POST /transactions/notify` webhook endpoint (`PayrexxNotifyTransactionResource`) binds the body via RESTEasy 4.7.7 + the Jackson JAX-RS provider, using the custom `ObjectMapper` from `RESTContextResolver` (`@Provider @Singleton`) built in `ObjectMapperFactory`. That mapper sets `FAIL_ON_UNKNOWN_PROPERTIES = false`, which is why `Transaction`s `@JsonAnySetter` catch-all works and why adding new response fields never breaks binding.

Consequence 1 (decisive): the Jackson provider is chosen only for `application/json`. If Payrexx POSTs `application/x-www-form-urlencoded`, RESTEasy returns **415 Unsupported Media Type** and the handler never runs. The webhook is registered with `type("json")` in `MerchantService.java:115`, so JSON is expected — but confirm against a real dev webhook.

Consequence 2: unknown top-level keys are silently ignored unless captured by `@JsonAnySetter`; nested known fields (like `metadata`) bind to their declared type (`Object` -> LinkedHashMap).

Related: [[Payrexx decline code lives at transaction.metadata.decline_code]], [[Payrexx notify webhook dispatches to two consumers, neither forwards decline code]].

## Related

- [[Payrexx decline code lives at transaction.metadata.decline_code]]
- [[1 Projects/luz_online_payment/Payrexx notify webhook dispatches to two consumers, neither forwards decline code]]

%% ai-graph-start %%

**Related notes:**
- [[luz_online_payment notify webhook silently 400-rejects ~43% of Payrexx webhooks on dev]]
- [[Payrexx notify webhook dispatches to two consumers, neither forwards decline code]]
- [[DeclineCodes resolver misses nested metadata.decline_code]]
- [[KlaraPay DTOs are code-blind - lenient Jackson drops any Payrexx decline code]]
- [[TransactionStatus.from returns null on unknown Payrexx status causing silent NotNull 400]]

**Relations:**
- luz_online_payment — *uses* — Payrexx webhook
- Payrexx webhook — *uses* — Jackson mapper
- Jackson mapper — *is configured with* — FAIL_ON_UNKNOWN_PROPERTIES off
- POST /transactions/notify — *is a* — webhook endpoint
- PayrexxNotifyTransactionResource — *binds* — POST /transactions/notify
- PayrexxNotifyTransactionResource — *uses* — RESTEasy
- RESTEasy — *integrates* — Jackson JAX-RS provider
- Jackson JAX-RS provider — *uses* — ObjectMapper
- ObjectMapper — *is from* — RESTContextResolver
- RESTContextResolver — *is built in* — ObjectMapperFactory
- ObjectMapper — *sets* — FAIL_ON_UNKNOWN_PROPERTIES = false
- FAIL_ON_UNKNOWN_PROPERTIES = false — *enables* — @JsonAnySetter
- @JsonAnySetter — *handles* — unknown top-level keys
- Jackson provider — *supports* — application/json
- Payrexx — *sends* — application/x-www-form-urlencoded
- application/x-www-form-urlencoded — *results in* — 415 Unsupported Media Type
- webhook — *is registered with type* — json
- webhook — *is registered in* — MerchantService.java:115
- Transaction — *has field* — metadata
- metadata — *binds to* — LinkedHashMap
- decline_code — *is located at* — transaction.metadata
- Payrexx notify webhook — *dispatches to* — consumers
- consumers — *do not forward* — decline_code

%% ai-graph-end %%