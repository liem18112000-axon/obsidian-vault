---
ai_hash: 0249820416b7d95b
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-20
entities:
- Campaign-gate template
- Tenant feature gates
- luz_docs
- MaterializeGate
- ParallelizeGate
- NgramGate
- mt-receive/LUZ-156856 (branch)
- DualCache
- Migration campaign status (L1)
- Repository data check (L2)
- MigrationCampaignService
- Campaign.Status
- COMPLETED (status)
- safeCache
- Cache
- Repository
- SUBJECT (hardcoded string)
- Catalogue (enum)
- migration.model (package)
- TTL (Time To Live)
- Config keys
- docker-compose
- kubernetes-overlays system.properties
- Campaign
- luz_docs safeCache is a deliberate per-class private copy (note)
source: session 2026-07-20 LUZ-156314
status: seedling
tags:
- luz-docs
- migration
- cache
- gate-pattern
title: 'Campaign-gate template: cache then campaign status L1 then repository L2'
type: model
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

%% ai-graph-start %%

**Related notes:**
- [[luz-docs migration campaign per-tenant activation flow]]
- [[Campaign flag now guards materialize write path (isAllowedTenant)]]
- [[Campaign COMPLETED status is only trusted after re-verifying document state (truth-check gate)]]
- [[Migration campaign status can silently drift from real document state]]
- [[luz_docs migration campaigns retry on next tenant request, not cron]]

**Relations:**
- Campaign-gate template — *defines* — Tenant feature gates
- Tenant feature gates — *are in* — luz_docs
- Tenant feature gates — *include* — MaterializeGate
- Tenant feature gates — *include* — ParallelizeGate
- Tenant feature gates — *include* — NgramGate
- Tenant feature gates — *follow template from* — mt-receive/LUZ-156856 (branch)
- Campaign-gate template — *uses* — DualCache
- DualCache — *answers* — is this migration done for this tenant?
- DualCache — *uses* — Cache
- Cache — *is checked by* — safeCache
- safeCache — *returns complete on* — Cache hit
- Cache miss — *triggers* — Migration campaign status (L1)
- Migration campaign status (L1) — *uses* — MigrationCampaignService
- MigrationCampaignService — *checks* — Campaign.Status
- Campaign.Status — *can be* — COMPLETED (status)
- Migration campaign status (L1) — *throws triggers* — Repository data check (L2)
- Repository data check (L2) — *accesses* — Repository
- Repository data check (L2) — *is* — expensive
- Result — *written to* — Cache
- Cache — *has* — TTL (Time To Live)
- TTL (Time To Live) — *is 3600s when* — complete
- TTL (Time To Live) — *is 60s when* — incomplete
- SUBJECT (hardcoded string) — *must match* — Catalogue (enum)
- Catalogue (enum) — *is in* — migration.model (package)
- Catalogue (enum) — *is* — package-private
- Renaming Catalogue constant — *breaks* — gates
- Config keys — *derive from* — Catalogue (enum)
- Config keys — *are set in* — docker-compose
- Config keys — *are set in* — kubernetes-overlays system.properties
- Campaign — *runs based on* — Config keys
- Campaign-gate template — *related to* — luz_docs safeCache is a deliberate per-class private copy (note)
- safeCache — *related to* — luz_docs safeCache is a deliberate per-class private copy (note)

%% ai-graph-end %%