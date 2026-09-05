---
title: "leo-customer360 service dependency + health-probe map"
created: 2026-09-05
type: reference
status: seedling
source: "session 2026-09-05"
tags: [leo-customer360, health-check, ads-server, data-tracking-api, dependencies, ops]
---

# leo-customer360 service dependency + health-probe map

Dependency map + `/health` semantics for the leo-customer360 services (uat behind Caddy on `beta.leocdp.com`; prod `leocdp.com`). Caddy routes: `/`->frontend, `/c360api`->api (root_path, unstripped), `/auth`->Keycloak, `/ads`->ads-server, `/data`->tracking (stripped), `/jaeger`->trace UI.

| Service | Real dependencies | `/health` behavior |
|---|---|---|
| customer360-api | PostgreSQL | probes DB (`SELECT 1`); `{status, database:reachable, sso_login}`. Public: `/c360api/health`. (Note: still raises->500 if DB down, not yet graceful.) |
| ads-server | PostgreSQL (schema `leo_ads`) only. Redis is aspirational in docstrings but NOT wired (no `core/cache.py`; redis config unused) | probes DB; 200 `database:reachable` / **503** `status:error, database:unreachable`. Also has `/health/database`. |
| data-tracking-api | **S3** object storage (critical sink) + **Redis** (rate-limit + session counters, fail-open) | probes both; 200 ok / 200 **degraded** (redis down) / **503 error** (s3 down). Reports `s3`, `redis`, `storage_mode`. |

Cheap probes used: DB `SELECT 1`; S3 `client.list_buckets()` (also validates creds); Redis `client.ping()`. The tracking probes were added as `S3ObjectStorage.check_connection()` and `TrackingRequestProtection.ping()`; the FastAPI `/health` injects the storage/protection singletons via `Depends(get_storage/get_protection)`.

Landed on branch `feat/health-dependency-checks` (2026-09-05). Concept: [[Health checks should probe dependencies and split critical vs fail-open]]. Verify uat: [[Verify uat customer360-api health publicly at beta.leocdp.com/c360api/health]].

## Related

- [[Health checks should probe dependencies and split critical vs fail-open]]
