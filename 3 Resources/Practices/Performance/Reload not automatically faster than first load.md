---
ai_hash: 05f153ad16ceb1dc
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-18
entities: []
tags:
- performance
- web
- gotcha
- klara
- measurement
---

# Reload is not automatically faster than first load

Intuition says a reload hits warm cache and beats the first load. Not guaranteed — especially for **server-rendered** pages where wall-time is dominated by server think-time + streaming, not client asset caching.

Measured on klara eArchive (`performance.klara.tech`, full document loads, in-page `performance.now()` marks):

| load | FCP | all content | load event |
|---|---|---|---|
| first  | 696 ms | **1181 ms** | 3413 ms |
| reload | 620 ms | **1838 ms** | 3962 ms |

Reload's content paint was ~650 ms **slower** even though its FCP was quicker. Run-to-run + server-side variance swamped any cache win. 

**Takeaway:** never report a single reload sample as "the faster path". Take multiple samples per load type and compare medians before claiming a cache/warm-up effect. One sample proves nothing about direction.

Related: [[Measure component render timing with Playwright addInitScript]]

%% ai-graph-start %%

**Related notes:**
- [[Perf 800k tenant eArchive reload timing]]
- [[Measure component render timing with Playwright addInitScript]]
- [[eArchive perf test plan 5 scenarios, all automated by trace tool]]
- [[Timing PrimeFaces dialog opens trusted click + stale-guard the reused dialog node]]
- [[Dev eArchive baseline items in 6s but count badges take 22-41s]]

%% ai-graph-end %%