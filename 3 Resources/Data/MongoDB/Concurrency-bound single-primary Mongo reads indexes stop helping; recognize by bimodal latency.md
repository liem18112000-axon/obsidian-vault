---
ai_hash: ad88afe3b10ebcf4
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-26
entities: []
source: session 2026-06-26 eArchive concurrency investigation
status: seedling
tags:
- mongodb
- concurrency
- performance
- wiredtiger
- index
- diagnosis
- earchive
title: 'Concurrency-bound single-primary Mongo reads: indexes stop helping; recognize
  by bimodal latency'
type: concept
---

# Concurrency-bound single-primary Mongo reads: indexes stop helping; recognize by bimodal latency

When a workload is **concurrency-bound on a single Mongo primary**, adding indexes stops helping — the bottleneck is the number of simultaneous heavy reads contending for one node's CPU / WiredTiger cache / IO, not the cost of any one query.

**Diagnostic signature (how to recognize it):**
- **Bimodal latency** — the same query is fast in isolation but ~3× slower whenever it overlaps others, with no middle band. Fast-vs-slow split, not a spread. (eArchive: aggregate ~2.5 s isolated vs ~8 s overlapping; slow ops outnumbered fast ~2:1.)
- **Median invariant to index suite** — p50 barely moves (or worsens) as you add indexes; more/larger index trees shrink cache and can push it disk-bound.
- **High stddev ≈ mean** on the heavy endpoints; CPU near container ceiling.
- **Fan-out** — count ops-per-request (sweep-line over `[end − time-consuming, end]` intervals from access logs gives max-concurrent). One eArchive page = ~12 concurrent security aggregates to `readPreference=primary`.

**Why index is the wrong lever:** an index reduces *per-op* docs-examined; it does nothing about N ops queueing for one node.

**Levers that actually help:** cut fan-out (coalesce N aggregates into 1); precompute/cache the result (serve off materialized fields or luz_cache instead of re-aggregating); spread reads with `secondaryPreferred`; bulkhead/semaphore heavy ops; size WiredTiger cache to hold the hot working set.

**To prove it directly:** hold index state fixed, vary only concurrency (1 vs 5 vs 10 parallel) — if p50 climbs with concurrency while single-user stays flat, it's concurrency-bound.

## Related

- [[3 Resources/Work-Kepler/eArchive/eArchive load wall is the materialize security aggregate, not index coverage]]
- [[Non-interleaved cross-case benchmarks on shared cluster confound index effect with cache+load]]

%% ai-graph-start %%

**Related notes:**
- [[Non-interleaved cross-case benchmarks on shared cluster confound index effect with cache+load]]
- [[Production security count is already COUNT_SCAN (covered); benchmark query's FETCH is inherent (multikey+$or+$nin)]]
- [[Widening fan-out threads doesn't help once MongoDB is the count bottleneck]]
- [[O(N) scan cliffs when working set exceeds DB cache]]
- [[Don't benchmark a scan-bound query right after a mass delete (WiredTiger cache blowout)]]

%% ai-graph-end %%