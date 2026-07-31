---
ai_hash: f569cfca99d2b623
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-30
entities: []
source: PROD jwt-service investigation 2026-06-30
status: seedling
tags:
- luz
- jwt-service
- luztenant
- security-classes
- tokens
title: jwt-service token path synchronously calls luztenant /security-classes
type: concept
---

# jwt-service token path synchronously calls luztenant /security-classes

On the per-tenant token path (`GET /luzsec/api/{id}/access/tokens?type=individual-tenant`), **jwt-service makes a synchronous outbound HTTP call into `luztenant-service`** to fill in the tokens security classes. Call chain inside jwt-service:

`TokenService.tryToFillSecurityClassesForTokenGenerator` → `SecurityClassService.getIndividualSecurityClassCodes` → `LuzTenantClient.getIndividualSecurityClasses` → `GET http://luztenant-service:8080/luztenant/api/users/me/individual-tenants/{id}/security-classes`.

jwt-service bounds this call with a **30s Vert.x client timeout** (`io.vertx.core.impl.NoStackTraceTimeoutException: The timeout period of 30000ms has been exceeded`). luztenant-service is a Mongo-backed WildFly service; its `LuzSecurityClassCacheService` caches security classes (and logs `Exception occurred while getting security class from cache data. message: null` when the cache-load path fails).

**Why it matters:** this makes token minting fragile — a slowdown in luztenants `/security-classes` lookup directly stalls token creation for that path (it was the confirmed root cause of the 2026-06-30 PROD read-timeout incident). The security-class lookup on the token hot path is an architectural coupling worth revisiting (cache/precompute/async).

Related: [[jwt-service token endpoints and replicas (Luz prod)]], [[Downstream timeout must sit well below caller timeout (fail-fast ladder)]].

## Related

- [[jwt-service token endpoints and replicas (Luz prod)]]
- [[Downstream timeout must sit well below caller timeout (fail-fast ladder)]]

%% ai-graph-start %%

**Related notes:**
- [[Luz caller read-timeout settings to jwt-service]]
- [[jwt-service token endpoints and replicas (Luz prod)]]
- [[Downstream timeout must sit well below caller timeout (fail-fast ladder)]]
- [[luz_docs_statistic two-token model service-tenant vs per-tenant cache token]]
- [[Log red herrings enclosing class name and baseline-noise lines]]

%% ai-graph-end %%