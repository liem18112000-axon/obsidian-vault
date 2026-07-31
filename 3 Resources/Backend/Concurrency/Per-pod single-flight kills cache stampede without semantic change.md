---
ai_hash: 65bff11b8653f427
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-23
entities: []
source: session 2026-07-23 MaterializeGate stampede panel
status: seedling
tags:
- concurrency
- cache
- stampede
- single-flight
- java
title: Per-pod single-flight kills cache stampede without semantic change
type: howto
---

# Per-pod single-flight kills cache stampede without semantic change

Cache stampede (thundering herd) on a cold key: coalesce concurrent computations per key **inside each pod** with a `ConcurrentHashMap<String, CompletableFuture<V>>`. First thread to `putIfAbsent` becomes leader and runs the existing computation **unchanged**; followers await the leader's future with a short bounded timeout (~2x the expected compute) and fall back to the *safe default* on timeout/failure. Leader publishes to shared cache *before* completing the future, then removes the map entry in `finally` (exact-value `remove(key, mine)` + idempotent `complete(safeDefault)` as safety net) — so there is no gap window, no leak, no follower hang.

Why this shape wins: N pods bound total work to N computations per cold window (100+ -> 1 per pod) with ~60 lines and zero semantic change to the leader path — existing tests keep passing. Bounded await beats (a) unbounded blocking (thread-pool starvation when the compute degenerates, e.g. a multi-second Mongo count) and (b) instant-fallback (when the compute answers fast, followers get the *correct* answer and skip the slow fallback path — a net latency win).

Prerequisite for the fallback: one direction of wrongness must be safe (here gate=false -> legacy-but-correct path). Cross-pod dedup beyond N-per-window needs an atomic putIfAbsent/CAS in the shared cache — a plain get/put REST cache (luz_cache) cannot host a reliable distributed lock, so racy sentinels only ever bound work to #pods anyway; per-pod single-flight already achieves that.

Hardening from adversarial review: peek the shared cache once in the follower-timeout branch (another pod may have published while the local leader is stuck); log the timeout path at WARNING not FINE; catch `CancellationException`; keep the `putIfAbsent` inside the try-block ownership window.

## Related

- [[ManagedExecutorService.execute loses CDI request context]]
- [[Raising negative-cache TTL turns transient failures into long-lived poison]]

%% ai-graph-start %%

**Related notes:**
- [[Lock-based stampede control losers hit the cache before the winner fills it]]
- [[Mongo unique-index insert as CAS when the cache has no putIfAbsent]]
- [[Raising negative-cache TTL turns transient failures into long-lived poison]]
- [[Delete-then-stale-put race bounds cache invalidation freshness at full TTL]]
- [[Cache-epoch invalidation fails if the epoch is read through a local L1]]

%% ai-graph-end %%