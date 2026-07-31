---
title: "luz_docs_statistic two-token model: service-tenant vs per-tenant cache token"
created: 2026-06-11
type: model
status: seedling
source: "luz_docs_statistic repo analysis, session 2026-06-11"
tags: [luz, jwt, luz-cache, security, luz-docs-statistic]
---

# luz_docs_statistic two-token model: service-tenant vs per-tenant cache token

luz_docs_statistic uses two different bearer tokens depending on *whose* data it touches:

1. **Service-tenant token** — obtained via client_credentials from luzsec, cached as a static singleton in `JwtService` and refreshed on expiry. Used for everything under the service tenant: reads/writes on the `documentstatistics` collection and calls to luz_cache.
2. **Per-tenant token** — fetched from luz_cache under key `luz_docs_api_<tenantId>` (placed there earlier by luz_docs). Used to run the $facet aggregation on that tenant's own `documents` collection, because jsonstore scopes data access by the token's tenant.

Gotcha: if the cache entry is missing/expired, that tenant's statistic update fails for the round (the aggregate call can't authenticate as the tenant). The cache key is deleted in a `finally` after each tenant is processed, so it is effectively a one-shot handoff from luz_docs.

Why: the service tenant has no right to read tenant documents directly; impersonation is done by borrowing the tenant token that luz_docs parked in the distributed cache. See [[Luz services access MongoDB only through the luz_jsonstore REST API]].

## Related

- [[Luz services access MongoDB only through the luz_jsonstore REST API]]
- [[2 Areas/Kepler/Luz/luz-docs-statistic/luz_docs_statistic updates stats via 1-minute EJB timer over PubSub and $facet aggregation]]
