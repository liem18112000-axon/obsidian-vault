---
title: eArchive 800k bottleneck is view-controller not K
tags: [luz, earchive, performance, view-controller, parallelize, bottleneck]
created: 2026-07-16
---

# eArchive 800k bottleneck is view-controller, not K

Perf test 2026-07-16 on the 800k tenant (LiemCompany, `45b05710…`) with `LUZ_DOCS_PARALLELIZE_COUNT_PARTITIONS=12`. Measured `time-consuming` per endpoint across all 4 services.

## Finding
The parallelize count path (the thing K tunes) is **not** the bottleneck:
- mongo/jsonstore `documents/count`: ~300 ms avg, 2.5 s max
- luz-docs `documents/count` (K=12 fan-out + join): 1.9 s avg, ~49 s max cold
- luz-docs `documents/search`: ~1.1 s

The bottleneck is **luz-docs-view-controller**, which inflates the same work by 1–2 orders of magnitude:
- `letters/badge-count`: **872 s** max, 201 s avg
- `letters/search`: 400 s max · `documents/search`: 364 s max · `letters/count`: 212 s max
- `archives/directories/branded`: 206 s max

## Why (hypothesis)
view-controller aggregates **serially** — badge-count and letters/count fan a separate sub-count per folder × security-class and sum them in-process, so cost scales with folder×class combinations, not doc count. Downstream luz-docs/mongo each return fast; the wall time is the serial chaining in view-controller. Confirmed indirectly: during the slow ops CPU stayed 24–147m/pod and luz-docs `worker_thread_usage`=0% → **serial-wait-bound, not CPU/thread/mongo-bound**.

## Consequence
Tuning K (count-partitions) on luz-docs cannot fix eArchive load on 800k — the win is capped by view-controller's aggregation. Text search ("invoice") over 800k did not complete in >380 s (likely gateway timeout). Optimisation effort should move to view-controller: parallelise/batch its per-folder sub-calls, or push aggregation down into a single luz-docs/mongo query.

Related: [[eArchive request flow and log correlation (perf)]] · [[Luz K count-partitions env var]]
