---
ai_hash: ae1447b0a39d4ff7
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-05
entities: []
source: session 2026-06-05 luz_docs change-tracking design
status: seedling
tags:
- jaxrs
- resteasy
- gotcha
- luz-docs
title: Implementing a @Path-annotated interface auto-registers the class as a JAX-RS
  server resource
type: lesson
---

# Implementing a @Path-annotated interface auto-registers the class as a JAX-RS server resource

If a JAX-RS deployment uses an empty `Application` subclass (only `@ApplicationPath`, no `getClasses()` override), the container auto-scans for `@Path` classes. JAX-RS annotation inheritance means a concrete class implementing an interface that carries class-level `@Path` (typical for MicroProfile REST *client* interfaces) gets picked up and registered as a *server* resource — exposing an unintended, possibly unsecured endpoint at the interface's path.

Consequence: a delegating wrapper around a REST client must NOT `implements` the client interface. Give it the same method signatures without the interface, or use an explicit `getClasses()` registration so scanning can't grab it.

Hit in luz_docs: `JsonStoreMongoClient` has `@Path("/{tenant-id}/{collection}")` and `JAXRSConfiguration` is an empty Application — a wrapper implementing it would have become a live endpoint. See [[CDI decorators and interceptors never fire on MicroProfile REST client proxies]] and [[luz_docs JsonStore change tracking via client-layer wrapper and CDI async events]].

## Related

- [[CDI decorators and interceptors never fire on MicroProfile REST client proxies]]
- [[luz_docs JsonStore change tracking via client-layer wrapper and CDI async events]]

%% ai-graph-start %%

**Related notes:**
- [[Intercept an MP REST client by implementing its interface - unqualified inject resolves the wrapper, RestClient qualifier is the bypass]]
- [[CDI decorators and interceptors never fire on MicroProfile REST client proxies]]
- [[luz_docs JsonStore change tracking via client-layer wrapper and CDI async events]]
- [[OpenAPI @RequestBody mediaType is documentation-only; JAX-RS @Consumes controls content negotiation]]
- [[Luz services access MongoDB only through the luz_jsonstore REST API]]

%% ai-graph-end %%