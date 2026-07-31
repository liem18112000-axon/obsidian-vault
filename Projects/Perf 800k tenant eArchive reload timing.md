---
tags: [klara, performance, eArchive, measurement, project]
created: 2026-07-18
---

# Perf 800k-tenant eArchive reload timing (RUNS=10)

Measured `performance.klara.tech` eArchive full reload with the Playwright addInitScript harness, 10 samples. Export: `luz_docs/docs/performance-test-800k/end-to-end-video/`.

**Clean medians (n=8, dropped a 28.7 s stalled-reload outlier + 1 goto timeout):**
- FCP ~636 ms · all content (folders + 47 letters, one paint) ~**1015 ms** · `.folders-container.loaded` ~1289 ms · load event ~1601 ms.

**Findings worth keeping:**
1. **Server-streamed single paint** holds across every run — all six content milestones stamp the same ms within a run; no progressive fill. So "first 48 items" ≡ "all items" on this build.
2. **`load event` is bimodal** — mostly ~1.5–1.7 s, occasionally ~3.2 s (late sub-resource). Content is usable at ~1 s regardless. Never quote load-event as a single stable figure; report the spread.
3. **~20 % of reloads hit a severe hiccup** on the 800k tenant (1 of 10 never navigated in 60 s; 1 stalled to 28.7 s). This is a backend/latency reliability signal, separate from the render path.
4. Keeping the outlier leaves the **median** ~unchanged but blows max/spread to ~28 k ms — concrete argument for median + a trimmed view over mean.

Related: [[Measure component render timing with Playwright addInitScript]] · [[Reload not automatically faster than first load]]
