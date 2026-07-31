---
title: "Non-interleaved cross-case benchmarks on shared cluster confound index effect with cache+load"
created: 2026-06-26
type: lesson
status: seedling
source: "session 2026-06-26"
tags: [benchmark, methodology, mongodb, wiredtiger, index, gotcha]
---

# Non-interleaved cross-case benchmarks on shared cluster confound index effect with cache+load

Cross-case index benchmarks on the shared dev Mongo cluster (luz-mongodb01) ran as **contiguous ~13-min blocks at different wall-clock times, not interleaved**. That confounds the index effect with two time-varying factors: (a) unrelated concurrent load on the shared cluster during each block, and (b) WiredTiger cache state. Consequence: an *indexed* run can measure SLOWER than no_index on a path the index doesn't even serve — because 15 indexes (esp. large collated `_en` trees) evict hot document pages from cache, pushing the doc-scanning aggregate disk-bound.

**Rule:** trust a cross-case delta only when Δmean > ~1 stddev AND the slower case's samples are a *uniform* shift (not spiky). Sub-20% deltas on high-variance endpoints (here: aggregate, root-list, folder-drill whose stddev ≈ or > mean) are noise — to measure them you must interleave/round-robin the index states or repeat many times. A tight uniform shift with low stddev (e.g. documents/search 760→2700ms, σ=375) IS real and indicates a planner plan-change, not load.

## Related

- [[eArchive load wall is the materialize security aggregate]]
- [[not index coverage]]
