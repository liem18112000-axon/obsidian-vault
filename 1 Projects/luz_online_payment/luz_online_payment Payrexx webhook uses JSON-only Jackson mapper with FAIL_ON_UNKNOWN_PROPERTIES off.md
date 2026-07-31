---
title: "luz_online_payment Payrexx webhook uses JSON-only Jackson mapper with FAIL_ON_UNKNOWN_PROPERTIES off"
created: 2026-07-31
type: observation
status: seedling
source: "LUZ-157476 investigation 2026-07"
tags: [payrexx, webhook, resteasy, jackson, luz-online-payment, luz-157476]
---

# luz_online_payment Payrexx webhook uses JSON-only Jackson mapper with FAIL_ON_UNKNOWN_PROPERTIES off

The `POST /transactions/notify` webhook endpoint (`PayrexxNotifyTransactionResource`) binds the body via RESTEasy 4.7.7 + the Jackson JAX-RS provider, using the custom `ObjectMapper` from `RESTContextResolver` (`@Provider @Singleton`) built in `ObjectMapperFactory`. That mapper sets `FAIL_ON_UNKNOWN_PROPERTIES = false`, which is why `Transaction`s `@JsonAnySetter` catch-all works and why adding new response fields never breaks binding.

Consequence 1 (decisive): the Jackson provider is chosen only for `application/json`. If Payrexx POSTs `application/x-www-form-urlencoded`, RESTEasy returns **415 Unsupported Media Type** and the handler never runs. The webhook is registered with `type("json")` in `MerchantService.java:115`, so JSON is expected — but confirm against a real dev webhook.

Consequence 2: unknown top-level keys are silently ignored unless captured by `@JsonAnySetter`; nested known fields (like `metadata`) bind to their declared type (`Object` -> LinkedHashMap).

Related: [[Payrexx decline code lives at transaction.metadata.decline_code]], [[Payrexx notify webhook dispatches to two consumers, neither forwards decline code]].

## Related

- [[Payrexx decline code lives at transaction.metadata.decline_code]]
- [[Payrexx notify webhook dispatches to two consumers]]
- [[neither forwards decline code]]
