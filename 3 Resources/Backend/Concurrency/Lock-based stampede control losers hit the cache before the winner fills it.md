---
title: "Lock-based stampede control: losers hit the cache before the winner fills it"
created: 2026-07-23
type: lesson
status: seedling
source: "session 2026-07-23 gate stampede discussion"
tags: [concurrency, distributed-lock, cache, stampede]
---

# Lock-based stampede control: losers hit the cache before the winner fills it

Distributed-lock stampede control ("only lock winner computes, losers read the cache") has a built-in timing gap: **losers reach their cache read while the winner is still computing**, so on the very cold window the design exists for, the cache is empty at loser-read time. The fallback branch must define what N-1 losers do during the winner's compute window (800ms to minutes), or the design silently degenerates.

Three loser strategies, pick explicitly:
1. **Safe default immediately** (e.g. gate=false -> legacy path): simplest, safe if one wrong direction is harmless — but dumps the whole burst on the slow path even when the true answer arrives 800ms later.
2. **Poll the cache with a deadline** (interval ~100ms, cap ~2x expected compute, then safe default): losers get real answers; cost = distributed busy-wait against the cache service.
3. **Wait on lock release**: needs watch/notify support; plain get/put/delete caches cannot, so this collapses into (2).

Also mandatory for any lock design: lease/TTL on the lock (winner crash) sized against worst-case compute vs liveness; fail-OPEN when the lock service is down (fail-closed disables the feature cluster-wide); winner releases only after the cache put.

Rule of thumb: at 2-3 pods, per-pod single-flight ([[Per-pod single-flight kills cache stampede without semantic change]]) captures ~97% of the win with zero new infra — distributed locking earns its complexity only at high pod counts or when even #pods duplicate computations are too expensive.

## Related

- [[Per-pod single-flight kills cache stampede without semantic change]]
- [[Mongo unique-index insert as CAS when the cache has no putIfAbsent]]
