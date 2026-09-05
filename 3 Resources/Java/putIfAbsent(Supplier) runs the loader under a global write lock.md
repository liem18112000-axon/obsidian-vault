---
title: "putIfAbsent(Supplier) runs the loader under a global write lock"
created: 2026-08-26
type: lesson
status: seedling
source: "session 2026-08-26 luz_jsonstore MongoClientFactory"
tags: [java, concurrency, cache, locking, gotcha, luz-jsonstore]
---

# putIfAbsent(Supplier) runs the loader under a global write lock

A cache "compute-if-absent with a supplier" convenience can hide a concurrency trap: if it runs the value-supplier **while holding the caches global write lock**, then a *slow* value-load blocks reads and writes for **every other key**, not just the one being loaded.

Concrete case: `luz_jsonstore`s `SimpleCache.putIfAbsent(key, Supplier)` calls `supplier.get()` inside the `ReentrantReadWriteLock` write lock. If the supplier opens a Mongo connection (seconds under a bad network / `connectTimeoutMS`), all other tenants cache lookups stall behind it.

**Fix / decision:** for slow loaders that must stay concurrent across keys, dont use the supplier form. Use a plain `getIfPresent` fast path + a **per-key lock** around create-then-`put`, so different keys load in parallel while still opening exactly one value per key. Reserve `putIfAbsent(Supplier)` for cheap, non-blocking loaders.

## Related
[[LRU cache in Java via LinkedHashMap accessOrder + removeEldestEntry]]

## Related

- [[LRU cache in Java via LinkedHashMap accessOrder + removeEldestEntry]]
