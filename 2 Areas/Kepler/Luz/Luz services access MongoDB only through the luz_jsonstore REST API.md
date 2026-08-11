---
ai_hash: c16ad97b57f7dbc6
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-11
entities:
- Luz services
- MongoDB
- luz_jsonstore REST API
- Luz/KLARA microservices
- HTTP
- MicroProfile REST client interface
- MongoDBClient
- '@RegisterRestClient'
- Mongo operators
- javax.json JsonObjects
- Tenant isolation
- Auth
- bearer token
- tenant-id path segment
- Resilience
- '@Retry'
- ServiceUnavailableException
- JAX-RS Response
- Connection leaks
- Service wrappers
- luz_docs_statistic two-token model service-tenant vs per-tenant cache token
- client-side
- /luz_jsonstore/api/mdb/{tenant-id}/{collection}
- data
source: luz_docs_statistic repo analysis, session 2026-06-11
status: seedling
tags:
- luz
- mongodb
- jsonstore
- architecture
- microprofile
title: Luz services access MongoDB only through the luz_jsonstore REST API
type: concept
---

# Luz services access MongoDB only through the luz_jsonstore REST API

Luz/KLARA microservices never open a direct MongoDB connection. Every read/write/aggregate goes over HTTP to the luz_jsonstore service (`/luz_jsonstore/api/mdb/{tenant-id}/{collection}`) through a MicroProfile REST client interface (e.g. `MongoDBClient` with `@RegisterRestClient(configKey = "LUZ_JSONSTORE_MDB_HOST_PORT")`).

Consequences:
- Mongo operators ($set, $facet, $match…) are built as `javax.json` JsonObjects and shipped as the request body — there is no Mongo driver in the service.
- Tenant isolation and auth are enforced by jsonstore via the bearer token + tenant-id path segment, so the *token you use determines whose data you touch* (see [[2 Areas/Kepler/Luz/luz-docs-statistic/luz_docs_statistic two-token model service-tenant vs per-tenant cache token]]).
- Resilience is done client-side, e.g. `@Retry(retryOn = ServiceUnavailableException.class)` around each call.
- Service wrappers must close the JAX-RS `Response` (try-with-resources) to avoid connection leaks; clients send `Connection: close`.

## Related

- [[2 Areas/Kepler/Luz/luz-docs-statistic/luz_docs_statistic two-token model service-tenant vs per-tenant cache token]]

%% ai-graph-start %%

**Related notes:**
- [[luz_docs_statistic two-token model service-tenant vs per-tenant cache token]]
- [[Luz performance env cluster topology]]
- [[01 Overview]]
- [[Count _shard docs per tenant via in-pod Percona mongo shell on dev]]
- [[Implementing a @Path-annotated interface auto-registers the class as a JAX-RS server resource]]

**Relations:**
- Luz services — *access* — MongoDB
- Luz services — *access via* — luz_jsonstore REST API
- Luz/KLARA microservices — *do not open direct connection to* — MongoDB
- Luz/KLARA microservices — *communicate with* — luz_jsonstore REST API
- Luz/KLARA microservices — *communicate using* — HTTP
- Luz/KLARA microservices — *use* — MicroProfile REST client interface
- MicroProfile REST client interface — *example* — MongoDBClient
- MongoDBClient — *is annotated with* — @RegisterRestClient
- Mongo operators — *are built as* — javax.json JsonObjects
- luz_jsonstore REST API — *enforces* — Tenant isolation
- luz_jsonstore REST API — *enforces* — Auth
- Tenant isolation — *is enforced via* — bearer token
- Auth — *is enforced via* — bearer token
- Tenant isolation — *is enforced via* — tenant-id path segment
- Auth — *is enforced via* — tenant-id path segment
- bearer token — *determines access to* — data
- Resilience — *is handled* — client-side
- Resilience — *uses* — @Retry
- @Retry — *handles* — ServiceUnavailableException
- Service wrappers — *must close* — JAX-RS Response
- Closing JAX-RS Response — *prevents* — Connection leaks
- luz_jsonstore REST API — *is related to* — luz_docs_statistic two-token model service-tenant vs per-tenant cache token
- luz_jsonstore REST API — *provides endpoint* — /luz_jsonstore/api/mdb/{tenant-id}/{collection}

%% ai-graph-end %%