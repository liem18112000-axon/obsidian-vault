---
title: "CDI decorators and interceptors never fire on MicroProfile REST client proxies"
created: 2026-06-05
type: lesson
status: seedling
source: "session 2026-06-05 luz_docs change-tracking design"
tags: [cdi, microprofile, rest-client, gotcha, luz-docs]
---

# CDI decorators and interceptors never fire on MicroProfile REST client proxies

A CDI `@Decorator` (or `@AroundInvoke` interceptor binding) will NEVER fire on a MicroProfile REST client interface (`@RegisterRestClient` + `@Inject @RestClient`). The client proxy is a *synthetic bean* registered by the RESTEasy/MP-rest-client CDI extension, and the CDI spec applies decorators/interceptors only to *managed beans*. The failure mode is silent: you can list the decorator in `beans.xml`, deployment succeeds, and it simply never runs.

Working alternative: a delegating wrapper bean that holds the only `@Inject @RestClient` reference and mirrors the client's methods; all other injection sites inject the wrapper. See [[luz_docs JsonStore change tracking via client-layer wrapper and CDI async events]] and the contrast with `RetryableEnricherDecorator` in luz_docs, which works only because `DocumentEnricher` impls are normal managed beans.

Related gotcha when building that wrapper: [[Implementing a @Path-annotated interface auto-registers the class as a JAX-RS server resource]].

## Related

- [[luz_docs JsonStore change tracking via client-layer wrapper and CDI async events]]
- [[Implementing a @Path-annotated interface auto-registers the class as a JAX-RS server resource]]
