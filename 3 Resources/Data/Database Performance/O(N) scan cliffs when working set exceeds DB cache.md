---
ai_hash: e0ff08038df9a890
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
aliases:
- cache-miss cliff
- working set exceeds cache
created: 2026-06-20
entities: []
source: session 2026-06-20 LUZ-154613
status: seedling
tags:
- database
- performance
- mongodb
- wiredtiger
- caching
- scaling
title: O(N) scan cliffs when working set exceeds DB cache
type: concept
---

# O(N) scan cliffs when working set exceeds DB cache

A linear-time DB scan/count (O(N) — visit every matched index entry) has a **per-item constant that is not constant**: it depends on whether the page is already in the DB's RAM cache (e.g. MongoDB WiredTiger). RAM hit ~100 ns; disk (SSD) miss ~100 µs — a ~1000× gap. While the working set fits cache, every access is a RAM hit → shallow slope. Once the working set exceeds cache, the DB evicts and re-reads pages from disk (thrashing) → the per-item cost climbs toward the disk number → latency **cliffs** at the threshold. Same O(N), constant multiplied by the cache-miss penalty — not a complexity change.

**Diagnostic signature: CPU collapses while latency explodes.** Cache-resident work is CPU-bound (cores busy comparing in-RAM keys, ~full cores). Disk-bound work leaves cores idle waiting on I/O → CPU drops to near zero. So *low CPU + high latency = working-set-exceeds-cache*, not a code regression.

**Corollary — thread-level parallelism stops helping past the cliff.** Splitting the scan across K threads only parallelises CPU work; K threads all issue reads into the *same* disk queue and contend, so fan-out gives no speedup once disk-bound. (Real parallelism past the cliff needs sharding across machines, each holding its slice in its own cache.)

**Fixes keep the constant small (don't change O(N)):** more cache RAM / bigger node so the set stays resident; a leaner/covering index (more entries per cached page → threshold pushed out); or shard so each node caches only its slice. To truly beat O(N) you must precompute (maintained counters), not scan.

Observed concretely in LUZ-154613 count benchmark: 720k docs = 11.5 s (~16 µs/doc, CPU-bound, ~4 cores) → 960k = 116 s (~125 µs/doc, CPU <250 m) — an ~8× per-doc jump = the disk penalty. See [[reference_count_fanout_K_benchmark]] (memory), and the count-fanout benchmark report.

## Related

- [[3 Resources/Visual/Excalidraw/Excalidraw data line charts need roundness null, not spline]]

%% ai-graph-start %%

**Related notes:**
- [[luz_docs documentscount is scan-bound and cannot reach sub-second at 128k]]
- [[Production security count is already COUNT_SCAN (covered); benchmark query's FETCH is inherent (multikey+$or+$nin)]]
- [[Widening fan-out threads doesn't help once MongoDB is the count bottleneck]]
- [[Shard count fan-out most of the win is at K=4, diminishing returns after]]
- [[MongoDB $facet buckets add no parallelism and defeat COUNT_SCAN]]

%% ai-graph-end %%