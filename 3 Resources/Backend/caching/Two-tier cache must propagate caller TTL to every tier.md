---
title: "Two-tier cache must propagate caller TTL to every tier"
created: 2026-07-10
type: lesson
status: seedling
source: "luz_docs parallelize code review, 2026-07-09/10"
tags: [caching, ttl, two-tier-cache, gotcha, code-review]
---

# Two-tier cache must propagate caller TTL to every tier

A two-tier cache wrapper (fast in-process L1 + shared/distributed L2) is only as correct as its weakest tier's TTL handling — if the L1 tier is built with one fixed expiry regardless of what TTL the caller asks for, the wrapper silently breaks its own advertised TTL contract in both directions.

Concretely: a `DualCache` wrapper's L1 (an in-process cache) is constructed once with a hardcoded fixed expiry (e.g. 300s), while its `put(..., ttl)` method forwards the caller's real per-call `ttl` only to L2 (the distributed tier) — L1's `put` call carries no TTL parameter at all. Since `get()` checks L1 first and only falls through to L2 on an L1 miss, this produces two failure modes depending on which side of 300s the intended TTL falls:
- **Intended TTL < L1's fixed expiry** (e.g. caller asks for 60s): a stale value keeps being served straight from L1 for up to 300s — 5x longer than intended — because L2 (which correctly expired at 60s) is never even consulted while L1 still has an entry.
- **Intended TTL > L1's fixed expiry** (e.g. caller asks for 3600s): L1 evicts early at 300s, forcing extra unnecessary L2 round-trips far more often than the caller intended (perf cost only, not staleness, since L2 still holds the correct value).

The dangerous part: this bug is **invisible from the call site**. The caller passes a TTL, the API accepts it, nothing looks wrong — you only find the mismatch by reading the cache implementation itself and checking whether every tier actually receives and applies the TTL argument.

**Rule of thumb:** when building or auditing a multi-tier cache, verify the TTL argument is threaded through to *every* tier's underlying store, not just the outermost/most-authoritative one. If a tier can't support per-entry TTLs, either size its fixed expiry to the shortest TTL any caller will ever request, or document plainly that its TTL is independent and callers shouldn't rely on the advertised value being tier-uniform.

Found in luz_docs' `ch.klara.luz.docs.cache.DualCache`, used by a sharding-completion gate that needs a 60s recheck for 'incomplete' vs a 3600s cache for 'complete' — the 60s case was actually taking ~300s to recheck.
