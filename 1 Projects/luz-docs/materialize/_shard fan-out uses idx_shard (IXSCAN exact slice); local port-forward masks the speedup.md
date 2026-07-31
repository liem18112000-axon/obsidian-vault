---
title: "_shard fan-out uses idx_shard (IXSCAN exact slice); local port-forward masks the speedup"
created: 2026-06-16
type: lesson
status: seedling
source: "LUZ-154613 session 2026-06-16"
tags: [luz-docs, materialize, mongodb, index, performance, testing]
---

# _shard fan-out uses idx_shard (IXSCAN exact slice); local port-forward masks the speedup

luz-docs LUZ-154613 _shard fan-out — explain on dev mongo (idx_shard {_shard:1}) for a quarter range {_shard:{$gte:0,$lt:2^28}}: IXSCAN, keysExamined=docsExamined=nReturned=31850 (exact slice, no over-scan), buckets balanced ~32k each. So the int-shard index design works: each sub-count is a true index seek of its slice.

Caveat for LOCAL testing: a docker-compose luz-docs reaching dev jsonstore goes through ONE kubectl port-forward → api-forwarder. K concurrent sub-counts serialize on that single pipe, so local wall-clock (~8.6s for K=4) is transport-dominated and does NOT reflect deployed performance — don't judge the fan-out speedup from a local port-forwarded run. Validate speed via mongo executionStats (countDocuments → COUNT_SCAN, keys only, no FETCH) or a deployed environment. A find().explain() over the tunnel is also misleading because it FETCHes + transfers full docs (31850 large docs ≈ 3s of transfer), unlike a count.

## Related

- [[Partition the materialized count on a uniform _shard int]]
- [[not _id]]
