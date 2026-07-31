---
ai_hash: 5a5701ea705c370b
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
aliases:
- 'luz_docs benchmark: full count scan is a dead end for sub-second targets'
created: 2026-07-09
entities:
- luz_docs
- POST /documents/count
- scan-bound
- sub-second performance
- 128k documents
- 14.1s median (K=1)
- _shard-range fan-out
- 3.15s median (K=6)
- 500ms target
- MongoDB primary contention
- application thread starvation
- replica-set primary
- non-sharded cluster
- executor pool
- 64 threads
- K=6 floor
- scanning
- epoch-keyed result cache
- index-covered COUNT_SCAN
- indexed boolean sentinel
- maintained cardinality structure
- exact counter
- Roaring bitmap
- HyperLogLog sketch
- estimated-count feature
- luz_docs countN badge
- fuzzy-zone fallback
- luz_docs estimated-count POC
- CAS
- backfill gate
- HyperLogLog error
- linear-counting regime
source: docs/perf-LUZ-154613-count-fanout-EXEC-SUMMARY.md + perf-LUZ-154613-count-scaling-findings-and-solution.md,
  session 2026-07-09
status: seedling
tags:
- luz-docs
- kepler
- mongodb
- performance
- benchmark
- count-optimization
title: luz_docs /documents/count is scan-bound and cannot reach sub-second at 128k
type: observation
---

# luz_docs /documents/count is scan-bound and cannot reach sub-second at 128k

Benchmark on a 128k-document luz_docs tenant, `POST /documents/count` (materialized read path): a single count (K=1) takes **14.1s median**; the existing `_shard`-range fan-out at **K=6 gives 3.15s median**. Both are far over a 500ms target.

The fan-out floor (~3s regardless of K) is **MongoDB primary contention**, not application thread starvation: all K sub-counts run concurrently against the *same* replica-set primary on a non-sharded cluster and serialize once its capacity saturates. Confirmed by widening the executor pool to 64 threads — it did not beat the K=6 floor. The speedup also decays with data size (4.5x at 128k, ~1x by 480k) because the scan is never removed, only spread across cores.

**Implication:** scanning the matching set — serial or parallel — is a dead end for sub-second counts. The only paths to the target stop scanning per request: an epoch-keyed result cache, an index-covered `COUNT_SCAN` (materialize the predicate into an indexed boolean sentinel so `count()` reads index keys with no document FETCH), or a maintained cardinality structure (exact counter, Roaring bitmap, or an approximate HyperLogLog sketch). This is the fact that justified investigating HLL for the estimated-count feature at all.

## Related

- [[1 Projects/luz-docs/luz_docs countN badge can use HyperLogLog with a fuzzy-zone fallback]]
- [[luz_docs estimated-count POC drops CAS and backfill gate]]
- [[3 Resources/Data/Algorithms/HyperLogLog error in the small-range (linear-counting) regime]]

%% ai-graph-start %%

**Related notes:**
- [[luz_docs countN badge can use HyperLogLog with a fuzzy-zone fallback]]
- [[luz-docs documentscount is ~130s on an 800k tenant — the 16-shard fan-out, not counting, is the bottleneck]]
- [[Frozen JsonStore gateway makes _id-range count fan-out a dead end — pivot to bitmapHLL]]
- [[Production security count is already COUNT_SCAN (covered); benchmark query's FETCH is inherent (multikey+$or+$nin)]]
- [[eArchive count baseline latency on dev ~80s for 128k docs (fan-out off)]]

**Relations:**
- luz_docs — *has operation* — POST /documents/count
- POST /documents/count — *is* — scan-bound
- POST /documents/count — *cannot reach* — sub-second performance
- POST /documents/count — *tested at* — 128k documents
- POST /documents/count — *takes* — 14.1s median (K=1)
- _shard-range fan-out — *applied to* — POST /documents/count
- _shard-range fan-out — *results in* — 3.15s median (K=6)
- 14.1s median (K=1) — *exceeds* — 500ms target
- 3.15s median (K=6) — *exceeds* — 500ms target
- K=6 floor — *caused by* — MongoDB primary contention
- MongoDB primary contention — *is not* — application thread starvation
- MongoDB primary contention — *occurs on* — replica-set primary
- replica-set primary — *is part of* — non-sharded cluster
- executor pool — *widened to* — 64 threads
- widening executor pool — *did not beat* — K=6 floor
- scanning — *is a* — dead end
- dead end — *for* — sub-second performance
- epoch-keyed result cache — *is a solution for* — sub-second performance
- index-covered COUNT_SCAN — *is a solution for* — sub-second performance
- index-covered COUNT_SCAN — *uses* — indexed boolean sentinel
- maintained cardinality structure — *is a solution for* — sub-second performance
- maintained cardinality structure — *includes* — exact counter
- maintained cardinality structure — *includes* — Roaring bitmap
- maintained cardinality structure — *includes* — HyperLogLog sketch
- HyperLogLog sketch — *justified investigation of* — estimated-count feature
- luz_docs countN badge — *can use* — HyperLogLog sketch
- luz_docs countN badge — *can use* — fuzzy-zone fallback
- luz_docs estimated-count POC — *drops* — CAS
- luz_docs estimated-count POC — *drops* — backfill gate
- HyperLogLog error — *occurs in* — linear-counting regime

%% ai-graph-end %%