---
ai_hash: dc86786148579a2b
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-23
entities: []
source: luz-docs LUZ-154613, sessions 2026-06-17 / 06-23 / 06-26
status: seedling
tags:
- mongodb
- indexing
- esr
- luz-docs
- count-fanout
- fanout
- sharding
- performance
- gotcha
title: Count fan-out _shard index must put _shard LAST in the compound key (ESR)
type: lesson
---

# Count fan-out _shard index must put _shard LAST in the compound key (ESR)

Splitting one `count(query)` into K concurrent sub-counts over disjoint `_shard` ranges is a **pessimization until** a compound index carries the base-query predicate fields first and the range field **`_shard` LAST** — the **ESR rule** (Equality, Sort, Range). Mongo uses **one index per count**: if `_shard` is not the trailing key of the index that serves the base predicate, every sub-count re-scans the full base result set and post-filters, so K sub-counts do ~K× the original work (with no index at all: **K concurrent COLLSCANs** contending for one cache). A stand-alone `{_shard: 1}` index does not rescue a filtered count — the base predicate must be satisfied by the *same* index.

luz-docs `documents`, the three materialize count indexes:
- `idx_shard` = `{_shard: 1}` — no-filter count
- `idx_isPublic_shard` = `{_isPublic: 1, _shard: 1}` (partialFilterExpression `{_isPublic: true}`)
- `idx_effectiveSecurityClassCodes_shard` = `{_effectiveSecurityClassCodes: 1, _shard: 1}`

Caveat: a range/sort field placed *before* `_shard` in the key still blocks a pure seek on the shard range.

**Correctness is index-independent** — the K ranges plus a `{_shard: {$exists: false}}` bucket tile every document disjointly, so the sum is exact even mid-backfill. Only speed depends on the index.

**Pre-flight checks before enabling fan-out:** (1) index in place with the bucket field trailing; (2) all documents actually stamped, else everything lands in the `$exists:false` bucket and K gives zero parallelism; (3) remember `{$exists:false}` is **non-indexable** by ordinary/sparse indexes, so a completeness gate probing it COLLSCANs on every cache miss — read completeness from the migration campaign status instead.

**Ceiling:** fan-out parallelizes work, it does not reduce it — ~K× wall-clock only while data is cache-resident; past the disk-bound wall K stops scaling. Count-only (`ToIntFunction<JsonObject>` → int); facet buckets need a per-bucket-merge variant.

luz-docs `parallelize` package: `ParallelizePartitioner` / `ParallelizeCount`, SHARD_SPACE = 2^30, K clamped to 64, config `luz.docs.parallelize.count-partitions` (default 1 = off). `createIndex` is idempotent on identical name+key; the `earchive-materialize-index` skill builds these and skips key-shape matches (`FORCE=1` to recreate).

## Related

- [[MongoDB ESR rule]]
- [[3 Resources/Data/MongoDB/Mongo facet $group count index only helps the $match prefix, not the count]]
- [[Random shard key gives balanced fan-out partitions (equal-width = equal-work only if uniform)]]
- [[Don't share one predicate between a read-path gate and a backfill selector]]

%% ai-graph-start %%

**Related notes:**
- [[Production security count is already COUNT_SCAN (covered); benchmark query's FETCH is inherent (multikey+$or+$nin)]]
- [[Divide-and-Conquer Visible-Document Count]]
- [[_shard fan-out uses idx_shard (IXSCAN exact slice); local port-forward masks the speedup]]
- [[luz-docs parallelized count undercounts documents missing _shard]]
- [[Fan-out count needs an explicit key-absent sub-count to stay exact during shard backfill]]

%% ai-graph-end %%