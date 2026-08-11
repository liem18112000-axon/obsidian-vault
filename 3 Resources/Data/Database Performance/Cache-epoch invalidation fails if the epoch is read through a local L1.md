---
ai_hash: f28f9e502398df53
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
aliases:
- epoch invalidation L1 staleness
created: 2026-06-20
entities: []
source: session 2026-06-20 LUZ-154613 code review
status: seedling
tags:
- caching
- invalidation
- distributed-systems
- dualcache
- multi-pod
- gotcha
- luz-docs
title: Cache-epoch invalidation fails if the epoch is read through a local L1
type: lesson
---

# Cache-epoch invalidation fails if the epoch is read through a local L1

**Pattern:** invalidate a derived cache by keying every entry under a per-tenant *epoch* token (`prefix + epoch + sha256(query)`); to invalidate, bump the epoch so all old keys are orphaned. Clean and lock-free — *if* every node sees the new epoch immediately.

**The trap:** if the epoch itself is read through a multi-tier cache whose first tier is a **per-process L1 with its own TTL**, cross-node invalidation is silently defeated. The bumping pod writes the new epoch to its L1 + the shared L2; every *other* pod keeps the OLD epoch in its own L1 until that entry expires, so it recomputes the OLD key and serves stale data for up to the L1 TTL. The long TTL on the *value* entries is a red herring — cross-node invalidation latency = the L1 TTL on the *epoch*.

**Fix:** read the epoch — and anything that must be observed immediately after another node's write — straight from the distributed tier, bypassing and **not populating** the L1. Value entries can still use L1: they're epoch-keyed, so a new epoch is a new key and there is no stale-value hazard; only the epoch lookup must skip L1.

**General rule:** an invalidation signal must never be cached more weakly than the data it invalidates. If readers can cache the signal, invalidation latency = the signal's cache TTL, not "immediate".

**luz-docs instance (LUZ-154613):** `MaterializeCountCache` keyed counts by an epoch read via `DualCache.get` (L1 = 300s `SimpleCache` in `MaterializeCache`, then L2 `CacheService`); `bumpCountEpoch` only refreshed the writer's L1, so other pods served stale counts for ~5 min. Fixed by adding `DualCache.getDistributed` (L2-only, no L1 populate) and reading the epoch through it. If ≤300s cross-pod staleness is genuinely acceptable (the tradeoff `MaterializeGate` already makes for materialisation-complete status), the stale count self-heals on L1 expiry — but that must be a deliberate decision, not an accident of routing.

## Related

- [[O(N) scan cliffs when working set exceeds DB cache]]
- [[Two-tier cache must propagate caller TTL to every tier]]
- [[Delete-then-stale-put race bounds cache invalidation freshness at full TTL]]
- [[DualCache L1 L2 near-cache pattern]]
- [[reference_count_api_and_K_rollout]]

%% ai-graph-start %%

**Related notes:**
- [[Two-tier cache must propagate caller TTL to every tier]]
- [[DualCache L1 write ignores per-call TTL (uses domain default)]]
- [[luz_docs stamps _shard on create to keep sharding gate stable]]
- [[Delete-then-stale-put race bounds cache invalidation freshness at full TTL]]
- [[Raising negative-cache TTL turns transient failures into long-lived poison]]

%% ai-graph-end %%