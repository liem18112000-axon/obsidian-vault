---
title: "Truncating DB collections between benchmark runs resets data but not service warmth"
created: 2026-08-13
type: lesson
status: seedling
source: "luz-docs-import run-1 cold-start investigation 2026-08-13"
tags: [benchmarking, performance, warm-up, jvm, gotcha]
---

# Truncating DB collections between benchmark runs resets data but not service warmth

Cleaning/truncating the database (e.g. `deleteMany({})` on the tenant collections) before each benchmark run gives a fresh **data** state — but it does **not** reset what actually makes a first run slow: the **JVM code cache (JIT-compiled methods)**, **connection state**, **downstream-service warmth** (their JIT, pools, caches, and e.g. clamd virus-definition load), and **in-process caches**. All of those persist across runs on the same pod.

So a cold first run stays slow no matter how thoroughly you truncate — the differentiator is **service warmth, not data**. When you see "run 1 much slower than runs 2+ despite cleaning between runs", suspect warm-up: either **discard run 1** or add an explicit **warm-up pass** before timing. Truncation only guarantees a comparable *data* starting point across runs; it says nothing about the process/JVM/downstream being equally warm.

Related: [[A latency penalty in the tail not the median points to JIT/GC warm-up under CPU contention]], [[Small per-run batches warm the JIT gradually across runs; large batches warm within one run]].

## Related

- [[A latency penalty in the tail not the median points to JIT/GC warm-up under CPU contention]]
- [[Small per-run batches warm the JIT gradually across runs; large batches warm within one run]]
