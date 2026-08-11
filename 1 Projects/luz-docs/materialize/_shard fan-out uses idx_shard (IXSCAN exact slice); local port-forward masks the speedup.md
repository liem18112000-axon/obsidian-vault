---
ai_hash: f80a42e1e8ec5620
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-16
entities:
- _shard fan-out
- idx_shard
- IXSCAN exact slice
- local port-forward
- speedup
- luz-docs
- LUZ-154613
- dev mongo
- _shard (index field)
- quarter range
- IXSCAN
- keysExamined=docsExamined=nReturned=31850
- exact slice
- over-scan
- buckets
- int-shard index design
- sub-count
- index seek
- slice
- LOCAL testing
- docker-compose luz-docs
- dev jsonstore
- kubectl port-forward
- api-forwarder
- K concurrent sub-counts
- single pipe
- local wall-clock
- transport-dominated
- deployed performance
- fan-out speedup
- mongo executionStats
- countDocuments
- COUNT_SCAN
- keys
- FETCH
- deployed environment
- find().explain()
- tunnel
- full docs
- transfer
- count
- Partition the materialized count on a uniform _shard int
- _id
source: LUZ-154613 session 2026-06-16
status: seedling
tags:
- luz-docs
- materialize
- mongodb
- index
- performance
- testing
title: _shard fan-out uses idx_shard (IXSCAN exact slice); local port-forward masks
  the speedup
type: lesson
---

# _shard fan-out uses idx_shard (IXSCAN exact slice); local port-forward masks the speedup

luz-docs LUZ-154613 _shard fan-out — explain on dev mongo (idx_shard {_shard:1}) for a quarter range {_shard:{$gte:0,$lt:2^28}}: IXSCAN, keysExamined=docsExamined=nReturned=31850 (exact slice, no over-scan), buckets balanced ~32k each. So the int-shard index design works: each sub-count is a true index seek of its slice.

Caveat for LOCAL testing: a docker-compose luz-docs reaching dev jsonstore goes through ONE kubectl port-forward → api-forwarder. K concurrent sub-counts serialize on that single pipe, so local wall-clock (~8.6s for K=4) is transport-dominated and does NOT reflect deployed performance — don't judge the fan-out speedup from a local port-forwarded run. Validate speed via mongo executionStats (countDocuments → COUNT_SCAN, keys only, no FETCH) or a deployed environment. A find().explain() over the tunnel is also misleading because it FETCHes + transfers full docs (31850 large docs ≈ 3s of transfer), unlike a count.

## Related

- [[Partition the materialized count on a uniform _shard int]]
- [[not _id]]

%% ai-graph-start %%

**Related notes:**
- [[Dev benchmark _shard count fan-out ~1.8x, diminishing past K=12; local port-forward hid the gain]]
- [[Shard count fan-out most of the win is at K=4, diminishing returns after]]
- [[Production security count is already COUNT_SCAN (covered); benchmark query's FETCH is inherent (multikey+$or+$nin)]]
- [[Partition the materialized count on a uniform _countShard int, not _id]]
- [[No existing luz-docs field works as a fan-out count partition key — survey]]

**Relations:**
- _shard fan-out — *uses* — idx_shard
- idx_shard — *is a type of* — IXSCAN exact slice
- local port-forward — *masks* — speedup
- luz-docs — *references* — LUZ-154613
- LUZ-154613 — *describes* — _shard fan-out
- _shard fan-out — *explained on* — dev mongo
- dev mongo — *uses* — idx_shard
- idx_shard — *uses field* — _shard (index field)
- quarter range — *specifies for* — _shard (index field)
- quarter range — *uses* — IXSCAN
- IXSCAN — *results in* — keysExamined=docsExamined=nReturned=31850
- IXSCAN — *is* — exact slice
- IXSCAN — *avoids* — over-scan
- buckets — *are* — balanced ~32k each
- int-shard index design — *works* — 
- int-shard index design — *enables* — sub-count
- sub-count — *is a* — true index seek of its slice
- LOCAL testing — *involves* — docker-compose luz-docs
- docker-compose luz-docs — *reaches* — dev jsonstore
- docker-compose luz-docs — *goes through* — kubectl port-forward
- kubectl port-forward — *connects to* — api-forwarder
- K concurrent sub-counts — *serialize on* — single pipe
- single pipe — *is* — kubectl port-forward
- local wall-clock — *is* — transport-dominated
- local wall-clock — *does not reflect* — deployed performance
- local port-forwarded run — *misrepresents* — fan-out speedup
- speed — *validated by* — mongo executionStats
- speed — *validated by* — deployed environment
- mongo executionStats — *uses* — countDocuments
- countDocuments — *uses* — COUNT_SCAN
- COUNT_SCAN — *examines* — keys
- COUNT_SCAN — *performs* — no FETCH
- find().explain() — *over* — tunnel
- find().explain() — *is* — misleading
- find().explain() — *FETCHes* — full docs
- full docs — *are* — 31850 large docs
- 31850 large docs — *causes* — transfer
- transfer — *takes* — 3s
- find().explain() — *differs from* — count
- _shard fan-out — *related to* — Partition the materialized count on a uniform _shard int
- _shard fan-out — *related to* — _id

%% ai-graph-end %%