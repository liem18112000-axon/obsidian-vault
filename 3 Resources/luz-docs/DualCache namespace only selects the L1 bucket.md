---
title: "DualCache namespace only selects the L1 bucket"
created: 2026-07-31
type: lesson
status: seedling
source: "session 2026-07-31"
tags: [luz-docs, java, caching, gotcha]
---

# DualCache namespace only selects the L1 bucket

`DualCache.of(domain)` — the `domain` string (callers pass `SomeClass.class.getName()`) only selects **which L1 SimpleCache bucket** is used (`L1_IDX` is keyed by `domain:ttl:maxSize:initCap`). It does **not** namespace the actual entry keys:

- L1 key = `tenantId@key` (see `buildL1Key`)
- L2 (DistributionCache) key = `token / tenantId / key`

Both are **domain-independent**. Consequence: two beans using different domains but the **same** `(tenantId, key)` would read/write the same L2 entry; conversely, distinct cache-key strings (e.g. `luz_docs_materialisation_complete` vs `luz_docs_parallelize_sharding_complete`) never collide even if they shared one domain. So merging or splitting the L1 domain is safe as long as the key strings differ.

File: `ch.klara.luz.docs.cache.DualCache`. Related: [[Extract shared root of near-identical CDI beans into a static common helper + Spec + supplier]].

## Related

- [[Extract shared root of near-identical CDI beans into a static common helper + Spec + supplier]]
