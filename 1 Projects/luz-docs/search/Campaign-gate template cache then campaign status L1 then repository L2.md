---
title: "Campaign-gate template: cache then campaign status L1 then repository L2"
created: 2026-07-20
type: model
status: seedling
source: "session 2026-07-20 LUZ-156314"
tags: [luz-docs, migration, cache, gate-pattern]
---

# Campaign-gate template: cache then campaign status L1 then repository L2

Tenant feature gates in luz_docs (MaterializeGate, ParallelizeGate, NgramGate) all follow one template from branch mt-receive/LUZ-156856: answer "is this migration done for this tenant?" via DualCache first, then L1 = migration campaign status, then L2 = repository data check.

Flow:
1. `safeCache(cache.get(...))` — cache hit returns immediately (`"1"` = complete).
2. Miss → **L1**: `MigrationCampaignService.findBySubject(jwt, tenantId, SUBJECT)` and compare `Campaign.Status.of(status) == COMPLETED`. Cheap read of the campaign record.
3. L1 throws → **L2** fallback: repository count check (`isMaterialized` / `isSharded` / `isTrigrammed`) — the expensive "count docs missing the field" query.
4. `finally` writes the result back to cache with split TTL: **3600s when complete, 60s when incomplete** (short negative-cache dampens repeat round-trips while backfill still runs).

Gotcha: `SUBJECT` is a hardcoded string (e.g. `"FULL_TEXT_SEARCH_TRIGRAMS"`) that must exactly match the `Catalogue` enum constant name — the enum is package-private to `migration.model`, so gates in other packages cannot reference it directly. Renaming a Catalogue constant silently breaks every gate that quotes it.

Related: [[luz_docs safeCache is a deliberate per-class private copy]]

## Related

- [[luz_docs safeCache is a deliberate per-class private copy]]

Config keys derive from the Catalogue constant name: `LUZ_DOCS_MIGRATION_CAMPAIGN_<NAME>_ENABLED` + `_INCLUDE_TENANT_IDS` / `_EXCLUDE_TENANT_IDS` (include supports `*`). Campaign runs only when enabled AND tenant included AND not excluded. Set in docker-compose for local, kubernetes-overlays system.properties for deployed envs.
