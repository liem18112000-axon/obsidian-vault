---
tags: [performance, web, gotcha, klara, measurement]
created: 2026-07-18
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
