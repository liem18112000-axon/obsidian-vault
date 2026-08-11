---
ai_hash: 7824b0c5840bf40b
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-31
entities: []
source: session 2026-07-31
status: seedling
tags:
- luz-docs
- java
- caching
- gotcha
title: DualCache namespace only selects the L1 bucket
type: lesson
---

# DualCache namespace only selects the L1 bucket

`DualCache.of(domain)` — the `domain` string (callers pass `SomeClass.class.getName()`) only selects **which L1 SimpleCache bucket** is used (`L1_IDX` is keyed by `domain:ttl:maxSize:initCap`). It does **not** namespace the actual entry keys:

- L1 key = `tenantId@key` (see `buildL1Key`)
- L2 (DistributionCache) key = `token / tenantId / key`

Both are **domain-independent**. Consequence: two beans using different domains but the **same** `(tenantId, key)` would read/write the same L2 entry; conversely, distinct cache-key strings (e.g. `luz_docs_materialisation_complete` vs `luz_docs_parallelize_sharding_complete`) never collide even if they shared one domain. So merging or splitting the L1 domain is safe as long as the key strings differ.

File: `ch.klara.luz.docs.cache.DualCache`. Related: [[Extract shared root of near-identical CDI beans into a static common helper + Spec + supplier]].

## Related

- [[Extract shared root of near-identical CDI beans into a static common helper + Spec + supplier]]

%% ai-graph-start %%

**Related notes:**
- [[Extract shared root of near-identical CDI beans into a static common helper + Spec + supplier]]
- [[DualCache L1 write ignores per-call TTL (uses domain default)]]
- [[Two-tier cache must propagate caller TTL to every tier]]
- [[luz_docs safeCache is a deliberate per-class private copy]]
- [[Cache-epoch invalidation fails if the epoch is read through a local L1]]

%% ai-graph-end %%