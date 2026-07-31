---
ai_hash: a0dd131c85ca48e7
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-16
entities:
- eArchive
- 800k tenant
- view-controller
- K parameter
- Perf test 2026-07-16
- LiemCompany
- Tenant ID 45b05710…
- LUZ_DOCS_PARALLELIZE_COUNT_PARTITIONS
- parallelize count path
- mongo/jsonstore
- documents/count (mongo/jsonstore)
- luz-docs
- documents/count (luz-docs)
- documents/search (luz-docs)
- luz-docs-view-controller
- letters/badge-count (view-controller)
- letters/search (view-controller)
- documents/search (view-controller)
- letters/count (view-controller)
- archives/directories/branded (view-controller)
- folder×class combinations
- serial-wait-bound
- CPU usage
- luz-docs worker_thread_usage
- CPU-bound
- thread-bound
- mongo-bound
- eArchive load
- view-controller aggregation
- Text search
- eArchive request flow and log correlation (perf)
- Luz K count-partitions env var
- parallelise/batch sub-calls
- push aggregation down
tags:
- luz
- earchive
- performance
- view-controller
- parallelize
- bottleneck
title: eArchive 800k bottleneck is view-controller not K
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

%% ai-graph-start %%

**Related notes:**
- [[luz-docs documentscount is ~130s on an 800k tenant — the 16-shard fan-out, not counting, is the bottleneck]]
- [[eArchive request flow and log correlation (perf)]]
- [[eArchive count baseline latency on dev ~80s for 128k docs (fan-out off)]]
- [[HPA replica scale-out cannot fix a serial wait that lives in another service]]
- [[eArchive load wall is the materialize security aggregate, not index coverage]]

**Relations:**
- eArchive — *HAS_BOTTLENECK* — view-controller
- eArchive — *NOT_BOTTLENECK* — K parameter
- Perf test 2026-07-16 — *TARGETED* — 800k tenant
- 800k tenant — *IS_COMPANY* — LiemCompany
- 800k tenant — *HAS_ID* — Tenant ID 45b05710…
- Perf test 2026-07-16 — *USED_ENV_VAR* — LUZ_DOCS_PARALLELIZE_COUNT_PARTITIONS
- LUZ_DOCS_PARALLELIZE_COUNT_PARTITIONS — *HAS_VALUE* — 12
- parallelize count path — *IS_TUNED_BY* — K parameter
- parallelize count path — *NOT_BOTTLENECK_FOR* — eArchive
- mongo/jsonstore — *PROVIDES_ENDPOINT* — documents/count (mongo/jsonstore)
- documents/count (mongo/jsonstore) — *HAS_MAX_TIME* — 2.5 s
- luz-docs — *PROVIDES_ENDPOINT* — documents/count (luz-docs)
- documents/count (luz-docs) — *HAS_MAX_TIME* — ~49 s
- luz-docs — *PROVIDES_ENDPOINT* — documents/search (luz-docs)
- documents/search (luz-docs) — *HAS_AVG_TIME* — ~1.1 s
- luz-docs-view-controller — *INFLATES_WORK_BY* — 1–2 orders of magnitude
- luz-docs-view-controller — *PROVIDES_ENDPOINT* — letters/badge-count (view-controller)
- letters/badge-count (view-controller) — *HAS_MAX_TIME* — 872 s
- luz-docs-view-controller — *PROVIDES_ENDPOINT* — letters/search (view-controller)
- letters/search (view-controller) — *HAS_MAX_TIME* — 400 s
- luz-docs-view-controller — *PROVIDES_ENDPOINT* — documents/search (view-controller)
- documents/search (view-controller) — *HAS_MAX_TIME* — 364 s
- luz-docs-view-controller — *PROVIDES_ENDPOINT* — letters/count (view-controller)
- letters/count (view-controller) — *HAS_MAX_TIME* — 212 s
- luz-docs-view-controller — *PROVIDES_ENDPOINT* — archives/directories/branded (view-controller)
- archives/directories/branded (view-controller) — *HAS_MAX_TIME* — 206 s
- view-controller — *AGGREGATES* — serially
- letters/badge-count (view-controller) — *SCALES_WITH* — folder×class combinations
- letters/count (view-controller) — *SCALES_WITH* — folder×class combinations
- eArchive bottleneck — *IS_TYPE* — serial-wait-bound
- eArchive bottleneck — *NOT_TYPE* — CPU-bound
- eArchive bottleneck — *NOT_TYPE* — thread-bound
- eArchive bottleneck — *NOT_TYPE* — mongo-bound
- CPU usage — *MEASURED_AT* — 24–147m/pod
- luz-docs worker_thread_usage — *MEASURED_AT* — 0%
- K parameter — *CANNOT_FIX* — eArchive load
- eArchive load — *CAPPED_BY* — view-controller aggregation
- Text search — *DID_NOT_COMPLETE_IN* — >380 s
- Optimisation effort — *SHOULD_FOCUS_ON* — luz-docs-view-controller
- luz-docs-view-controller — *OPTIMISATION_STRATEGY* — parallelise/batch sub-calls
- luz-docs-view-controller — *OPTIMISATION_STRATEGY* — push aggregation down
- eArchive — *RELATED_TO* — eArchive request flow and log correlation (perf)
- K parameter — *RELATED_TO* — Luz K count-partitions env var

%% ai-graph-end %%