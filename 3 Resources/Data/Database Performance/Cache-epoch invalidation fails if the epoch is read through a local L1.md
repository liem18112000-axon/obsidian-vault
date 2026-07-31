---
title: "Cache-epoch invalidation fails if the epoch is read through a local L1"
created: 2026-06-20
aliases: ["epoch invalidation L1 staleness"]
type: lesson
status: seedling
source: "session 2026-06-20 LUZ-154613 code review"
tags: [caching, invalidation, distributed-systems, gotcha, multi-pod]
---

# Cache-epoch invalidation fails if the epoch is read through a local L1

**Pattern:** invalidate a derived cache by keying every entry under a per-tenant *epoch* token; to invalidate, rotate (bump) the epoch so all old keys are orphaned. Clean and lock-free — *if* every node sees the new epoch immediately.

**The trap:** if the epoch value is itself read through a multi-tier cache whose first tier is a **per-process L1 with its own TTL**, the invalidation is silently defeated across nodes. The pod that bumps writes the new epoch to its L1 + the shared L2; every *other* pod keeps the OLD epoch in its own L1 until that L1 entry expires, so it rebuilds the OLD epoch-keyed value and serves stale data for up to the L1 TTL. The long TTL on the *value* entries is a red herring — the real cross-node invalidation latency is the L1 TTL on the *epoch*.

**Fix:** read the epoch (and any value that must be observed immediately after a write from another node) straight from the distributed tier, bypassing — and not populating — the L1. The value entries can still use L1 because they're epoch-keyed: a new epoch is a new key, so there's no stale-value hazard, only the epoch lookup must be L1-skipping.

**General rule:** an invalidation signal must never be cached more weakly than the data it invalidates. If readers can cache the signal, invalidation latency = the signal's cache TTL, not 'immediate'.

Found in LUZ-154613 review: luz-docs eArchive count cache (MaterializeCountCache) keyed counts by an epoch read via DualCache.get (L1 = 300s SimpleCache before L2); bumpCountEpoch only refreshed the writer's L1, so other pods served stale counts ~5 min. Fixed by adding DualCache.getDistributed (L2-only, no L1 populate) and reading the epoch through it. See [[O(N) scan cliffs when working set exceeds DB cache]] and [[reference_count_api_and_K_rollout]].

## Related

- [[O(N) scan cliffs when working set exceeds DB cache]]
