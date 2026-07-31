---
title: "Reconstruct request waterfall from undertow accesslog: start = timestamp minus time-consuming"
created: 2026-07-16
type: howto
status: seedling
source: "session 2026-07-16"
tags: [undertow, accesslog, waterfall, latency, chartjs, luz-docs]
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
