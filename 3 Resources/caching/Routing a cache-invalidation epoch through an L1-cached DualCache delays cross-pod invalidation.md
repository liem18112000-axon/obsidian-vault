---
title: "Routing a cache-invalidation epoch through an L1-cached DualCache delays cross-pod invalidation"
created: 2026-06-20
type: lesson
status: seedling
source: "session 2026-06-20 LUZ-154613"
tags: [caching, dualcache, invalidation, gotcha, luz-docs]
---

# Routing a cache-invalidation epoch through an L1-cached DualCache delays cross-pod invalidation

If a cache-invalidation **epoch** (a version token embedded in derived cache keys) is itself read through a two-tier DualCache (L1 in-process + L2 distributed), then bumping the epoch on one pod is **not** visible on other pods until their L1 copy of the epoch expires. Invalidation is therefore delayed by up to the L1 TTL instead of being immediate.

## Why
The count cache key is `prefix + epoch + sha256(query)`. Changing the epoch is the invalidation mechanism: a new epoch ⇒ new key ⇒ miss ⇒ recompute. But if the epoch read hits a per-pod L1 (300s TTL in `MaterializeCache`), pod B keeps computing the *old* key — and serving stale counts — until its L1 epoch entry expires (≤300s). When the epoch lived in L2 only, every pod saw the bump immediately.

## When it applies
Any epoch/generation-counter invalidation scheme where the counter is read through a near-cache. The values keyed by the epoch are safe to L1-cache; the **epoch itself** is the part where L1 trades away cross-pod immediacy.

## Decision taken (luz_docs, LUZ-154613)
`MaterializeCountCache` was switched from raw L2 (`CacheService`) to the `MaterializeCache` DualCache wrapper for reuse. Accepted the ≤300s cross-pod staleness — same tradeoff `MaterializeGate` already makes for the materialisation-complete status. Acceptable because a stale count self-heals on L1 expiry; flag it only if counts must invalidate instantly cross-pod.

Related: [[luz-docs materialize count cache]] [[DualCache L1 L2 near-cache pattern]]

## Related

- [[luz-docs materialize count cache]]
- [[DualCache L1 L2 near-cache pattern]]
