---
title: "leo-customer360: Redis is a fail-open cache/auth-cache/rate-limiter used only by customer360-api"
created: 2026-08-18
type: reference
status: seedling
source: "session 2026-08-18 customer360-api/core/cache.py"
tags: [customer360, redis, cache, fastapi, architecture]
---

# leo-customer360: Redis is a fail-open cache/auth-cache/rate-limiter used only by customer360-api

In the leo-customer360 stack, **Redis is used ONLY by `customer360-api`** (the FastAPI service) — `backend-system`/Dagster does not use it. It serves three purposes, all **fail-open** (if Redis is down/unset the app silently falls back — Redis is never a hard dependency for availability):

1. **GET response cache** (`core/cache.py`) — a `cache_response` decorator JSON-encodes any read-heavy GET route`s result and replays it on a hit; prefix-scoped invalidation (`invalidate_prefix`) runs on create/update/delete. Default TTL 60s. CAVEAT: backend-system writes to `cdp_*` tables via psycopg2 directly, bypassing the API, so those writes do NOT invalidate the cache — staleness is bounded by the TTL.
2. **Auth caching** (`core/auth.py`) — caches Keycloak tokens and resolved identities (`sys_userinfo`).
3. **Rate limiting** (`core/utils/rate_limiter.py`, `RedisRateLimiter`) — fixed-window throttle that counts only FAILED auth attempts (brute-force protection); successful requests are never throttled.

Config (pydantic settings / env): `REDIS_HOST` (default localhost), `REDIS_PORT` (default **6580**, non-standard), `REDIS_DB` (0), `REDIS_PASSWORD`, `CACHE_ENABLED` (default true), `CACHE_TTL_SECONDS` (60). In docker-compose it is a custom image (`./redis`), password-protected (`--requirepass`), 256MB cap, persistent `customer360-redisdata` volume.

DEPLOYMENT NOTE: when customer360-api is deployed WITHOUT setting REDIS_* and without a Redis instance (e.g. the single-VM docker run), it defaults to localhost:6580, finds nothing, and fails open -> caching + rate-limiting are effectively OFF and every request hits Postgres directly. The API still works; it just loses the cache/throttle. VNG offers managed Redis via vDB MemStore (`vngcloud_vdb_memstore_*`) if you want a real instance.
