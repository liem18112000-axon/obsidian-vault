---
ai_hash: 7fe802d6550dc1f8
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-16
entities: []
source: session 2026-07-16
status: seedling
tags:
- performance
- hpa
- autoscaling
- bottleneck
- luz-docs
- serial-wait
title: HPA replica scale-out cannot fix a serial wait that lives in another service
type: lesson
---

# HPA replica scale-out cannot fix a serial wait that lives in another service

Adding pod replicas (or letting an HPA scale out) only speeds up work that is **CPU/throughput-bound on that service**. It does nothing for a request whose wall-clock is spent **waiting serially on a downstream service**.

Observed in the luz-docs 800k eArchive perf test (trials 6-10): the luz-docs HPA scaled the StatefulSet 5 -> 6 -> 10 replicas on its own during the run, while luz-docs CPU sat at 25-72 m and worker-thread-pool usage was 0-2%. eArchive load time did NOT improve — T10 with 10 pods still ~256 s, and the view-controller `letters/badge-count` max stayed pinned at 872,317 ms every trial. The real bottleneck is `luz-docs-view-controller` serially aggregating per-folder / per-security-class sub-calls; luz-docs was never the constraint, so replicating it changed nothing.

Diagnostic tell: if CPU is low and the worker/thread pool is near-idle while latency is high, you are serial-wait-bound. Scaling out is the wrong lever — fix the serial fan-out (parallelise/batch it, or push the aggregation down into one query) in the service that actually blocks.

## Related
[[luz-docs 800k eArchive performance test]]
[[Reconstruct request waterfall from undertow accesslog start = timestamp minus time-consuming]]

## Related

- [[luz-docs 800k eArchive performance test]]

%% ai-graph-start %%

**Related notes:**
- [[eArchive 800k bottleneck is view-controller not K]]
- [[eArchive request flow and log correlation (perf)]]
- [[eArchive count baseline latency on dev ~80s for 128k docs (fan-out off)]]
- [[luz-docs documentscount is ~130s on an 800k tenant — the 16-shard fan-out, not counting, is the bottleneck]]
- [[Materialize gate cache never latches, hammers campaign service on every count]]

%% ai-graph-end %%