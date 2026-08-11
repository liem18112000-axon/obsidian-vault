---
ai_hash: b21db52c369bb522
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-16
entities: []
source: session 2026-07-16
status: seedling
tags:
- undertow
- accesslog
- waterfall
- latency
- chartjs
- luz-docs
title: 'Reconstruct request waterfall from undertow accesslog: start = timestamp minus
  time-consuming'
type: howto
---

# Reconstruct request waterfall from undertow accesslog: start = timestamp minus time-consuming

The `io.undertow.accesslog` line is emitted when a request **completes**, and carries `time-consuming=<ms>` (the wall duration). So a per-request START time is reconstructable: `start = log-timestamp − time-consuming`. That is enough to rebuild a browser-network-style waterfall from server logs alone — no client tracing needed.

Method:
1. Parse each accesslog line → `(service, method, endpoint, endMs, durMs)`; compute `startMs = endMs − durMs`.
2. Sort by `startMs`; subtract the earliest start → each request's offset on a relative timeline.
3. Overlapping `[start, end]` intervals = concurrent requests (different worker threads).
4. Render as Chart.js **horizontal floating bars**: `indexAxis:"y"`, each datum `= [startSec, endSec]`, color by service.

**Gotcha — bounded windows hide the tail:** a fixed log-fetch window (e.g. `freshness=5m`) only fully captures the fast/warm requests. Pathological long-running requests (e.g. a 872 s `letters/badge-count`) start before the window and/or end after it, span multiple capture windows, and will NOT sit cleanly inside one journey window. Show the warm fast-path waterfall for journey *shape*, and report the slow maxes separately from an aggregated latency table.

Applied in the luz-docs 800k eArchive perf report (`docs/performance-test-800k/end-to-end/report.html`, "User journey" section) — one warm ~18 s load, 3 request bursts separated by client-side PrimeFaces render gaps.

## Related
[[luz-docs 800k eArchive performance test]]

## Related

- [[luz-docs 800k eArchive performance test]]

%% ai-graph-start %%

**Related notes:**
- [[eArchive request flow and log correlation (perf)]]
- [[luz-docs API request bodies are only observable as downstream luz-jsonstore queries]]
- [[HPA replica scale-out cannot fix a serial wait that lives in another service]]
- [[Perf 800k tenant eArchive reload timing]]
- [[eArchive 800k bottleneck is view-controller not K]]

%% ai-graph-end %%