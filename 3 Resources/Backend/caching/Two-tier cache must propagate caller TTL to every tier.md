---
title: "Two-tier cache must propagate caller TTL to every tier"
created: 2026-07-10
type: lesson
status: seedling
source: "luz_docs parallelize code review, 2026-07-09/10"
tags: [caching, ttl, two-tier-cache, dualcache, gotcha, code-review]
---

# Two-tier cache must propagate caller TTL to every tier

A two-tier wrapper (in-process L1 + distributed L2) whose `put(key, value, ttl)` forwards the TTL only to L2 — while L1 was constructed once with a hardcoded fixed expiry — silently breaks its own advertised TTL contract in **both** directions, because `get()` consults L1 first and only falls through on an L1 miss:

- **Caller TTL < L1's fixed expiry** (asks 60s, L1 fixed 300s): stale values keep being served from L1 for up to 300s — 5× too long — while the correctly-expired L2 entry is never consulted. Staleness bug.
- **Caller TTL > L1's fixed expiry** (asks 3600s): L1 evicts at 300s, causing needless L2 round-trips. Perf cost only.

The bug is **invisible from the call site** — the API accepts the TTL and nothing looks wrong; you only find it by reading the implementation and checking that each tier's underlying store receives and applies the TTL argument.

**Rule:** thread the TTL to *every* tier. If a tier cannot do per-entry TTLs, size its fixed expiry to the shortest TTL any caller will request, or document that its expiry is independent.

Found in `ch.klara.luz.docs.cache.DualCache`, used by a sharding-completion gate needing a 60s recheck for "incomplete" vs 3600s for "complete" — the 60s recheck actually took ~300s.

## Related

- [[Cache-epoch invalidation fails if the epoch is read through a local L1]]
- [[Raising negative-cache TTL turns transient failures into long-lived poison]]
