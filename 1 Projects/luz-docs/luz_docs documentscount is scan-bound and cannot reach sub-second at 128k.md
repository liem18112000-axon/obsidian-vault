---
title: "luz_docs /documents/count is scan-bound and cannot reach sub-second at 128k"
aliases:
  - "luz_docs benchmark: full count scan is a dead end for sub-second targets"
created: 2026-07-09
type: observation
status: seedling
source: "docs/perf-LUZ-154613-count-fanout-EXEC-SUMMARY.md + perf-LUZ-154613-count-scaling-findings-and-solution.md, session 2026-07-09"
tags: [luz-docs, kepler, mongodb, performance, benchmark, count-optimization]
---

# luz_docs /documents/count is scan-bound and cannot reach sub-second at 128k

Benchmark on a 128k-document luz_docs tenant, `POST /documents/count` (materialized read path): a single count (K=1) takes **14.1s median**; the existing `_shard`-range fan-out at **K=6 gives 3.15s median**. Both are far over a 500ms target.

The fan-out floor (~3s regardless of K) is **MongoDB primary contention**, not application thread starvation: all K sub-counts run concurrently against the *same* replica-set primary on a non-sharded cluster and serialize once its capacity saturates. Confirmed by widening the executor pool to 64 threads — it did not beat the K=6 floor. The speedup also decays with data size (4.5x at 128k, ~1x by 480k) because the scan is never removed, only spread across cores.

**Implication:** scanning the matching set — serial or parallel — is a dead end for sub-second counts. The only paths to the target stop scanning per request: an epoch-keyed result cache, an index-covered `COUNT_SCAN` (materialize the predicate into an indexed boolean sentinel so `count()` reads index keys with no document FETCH), or a maintained cardinality structure (exact counter, Roaring bitmap, or an approximate HyperLogLog sketch). This is the fact that justified investigating HLL for the estimated-count feature at all.

## Related

- [[luz_docs count>N badge can use HyperLogLog with a fuzzy-zone fallback]]
- [[luz_docs estimated-count POC drops CAS and backfill gate]]
- [[HyperLogLog small-range error needs linear-counting correction]]
