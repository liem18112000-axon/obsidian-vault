---
ai_hash: 65e1c00ec7c092c3
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-17
entities: []
source: luz_docs LUZ-154613 2026-06-17
status: seedling
tags:
- mongodb
- performance
- aggregation
- indexing
title: MongoDB $facet buckets add no parallelism and defeat COUNT_SCAN
type: lesson
---

# MongoDB $facet buckets add no parallelism and defeat COUNT_SCAN

When parallelizing a slow MongoDB count by `_shard` ranges on a non-sharded replica set, the speedup comes ONLY from running M **concurrent** count operations (M connections -> Mongo gives each its own thread, scanning disjoint ranges in parallel). 

Packing N sub-counts into ONE aggregation via `$facet` (e.g. M threads each doing a facet of N -> K=M*N buckets) does NOT help latency:
- A `$facet` scans its input stream ONCE and fans each doc to the N facet counters in memory. The N split is cheap in-memory bucketing, not extra parallelism and not less scan. So one facet-of-N call ~= one plain count of that super-range. K=M*N behaves like K=M.

Two real but secondary effects of the facet approach:
- GOOD: fewer round-trips (M calls instead of K) -> less connection/`connection=close` overhead and less downstream load amplification (fewer jsonstore/Mongo calls per user count -> fewer 503/fail-loud).
- BAD: `$facet` must materialize docs to bucket them -> forces IXSCAN+FETCH, which DEFEATS the count-only `COUNT_SCAN` fast path. So $facet fights an index-covered count: once the predicate is covered, M plain `count`s beat a facet.

Corollary: the real sub-2s lever for a scan-bound count is making the scan cheap (cover the predicate so a plain `count` is a COUNT_SCAN) or caching it — NOT more buckets and NOT $facet. Also note `countDocuments` runs an aggregation (fetches) while the legacy `count` command can use COUNT_SCAN.

Related: [[Count fan-out _shard index must put _shard LAST in the compound key (ESR)]] · [[3 Resources/Practices/Performance/Shard count fan-out most of the win is at K=4, diminishing returns after]]

## Related

- [[Count fan-out _shard index must put _shard LAST in the compound key (ESR)]]
- [[3 Resources/Practices/Performance/Shard count fan-out most of the win is at K=4, diminishing returns after]]

%% ai-graph-start %%

**Related notes:**
- [[Mongo facet $group count index only helps the $match prefix, not the count]]
- [[BitmapHLL counts supersede fan-out; they don't combine with it]]
- [[Count fan-out _shard index must put _shard LAST in the compound key (ESR)]]
- [[Production security count is already COUNT_SCAN (covered); benchmark query's FETCH is inherent (multikey+$or+$nin)]]
- [[Shard count fan-out most of the win is at K=4, diminishing returns after]]

%% ai-graph-end %%