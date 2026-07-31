---
title: "Luz services access MongoDB only through the luz_jsonstore REST API"
created: 2026-06-11
type: concept
status: seedling
source: "luz_docs_statistic repo analysis, session 2026-06-11"
tags: [luz, mongodb, jsonstore, architecture, microprofile]
---

# Luz services access MongoDB only through the luz_jsonstore REST API

Luz/KLARA microservices never open a direct MongoDB connection. Every read/write/aggregate goes over HTTP to the luz_jsonstore service (`/luz_jsonstore/api/mdb/{tenant-id}/{collection}`) through a MicroProfile REST client interface (e.g. `MongoDBClient` with `@RegisterRestClient(configKey = "LUZ_JSONSTORE_MDB_HOST_PORT")`).

Consequences:
- Mongo operators ($set, $facet, $match…) are built as `javax.json` JsonObjects and shipped as the request body — there is no Mongo driver in the service.
- Tenant isolation and auth are enforced by jsonstore via the bearer token + tenant-id path segment, so the *token you use determines whose data you touch* (see [[luz_docs_statistic two-token model: service-tenant vs per-tenant cache token]]).
- Resilience is done client-side, e.g. `@Retry(retryOn = ServiceUnavailableException.class)` around each call.
- Service wrappers must close the JAX-RS `Response` (try-with-resources) to avoid connection leaks; clients send `Connection: close`.

## Related

- [[luz_docs_statistic two-token model: service-tenant vs per-tenant cache token]]
