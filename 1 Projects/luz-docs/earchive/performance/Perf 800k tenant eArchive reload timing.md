---
ai_hash: 55a51ab30c9b8eb9
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-18
entities:
- Perf 800k tenant eArchive reload timing
- performance.klara.tech
- eArchive full reload
- Playwright addInitScript harness
- luz_docs/docs/performance-test-800k/end-to-end-video/
- FCP
- all content (folders + 47 letters, one paint)
- .folders-container.loaded
- load event
- Server-streamed single paint
- first 48 items
- all items
- build
- sub-resource
- 800k tenant
- backend/latency reliability signal
- render path
- outlier
- median
- max/spread
- mean
- Measure component render timing with Playwright addInitScript
- Reload not automatically faster than first load
- 10 samples
- Clean medians
- n=8
- stalled-reload outlier
- goto timeout
- Content
- severe hiccup
tags:
- klara
- performance
- eArchive
- measurement
- project
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

%% ai-graph-start %%

**Related notes:**
- [[Reload not automatically faster than first load]]
- [[eArchive perf test plan 5 scenarios, all automated by trace tool]]
- [[Measure component render timing with Playwright addInitScript]]
- [[Dev eArchive baseline items in 6s but count badges take 22-41s]]
- [[Trace tool folder-drill waits 3min because folder view lacks Documents-Custom counters]]

**Relations:**
- Perf 800k tenant eArchive reload timing — *is a topic of* — note
- eArchive full reload — *measured on* — performance.klara.tech
- eArchive full reload — *measured with* — Playwright addInitScript harness
- eArchive full reload — *measured with* — 10 samples
- luz_docs/docs/performance-test-800k/end-to-end-video/ — *is export location for* — eArchive full reload
- Clean medians — *based on* — n=8
- n=8 — *derived from* — 10 samples
- n=8 — *excludes* — stalled-reload outlier
- n=8 — *excludes* — goto timeout
- FCP — *is approximately* — 636 ms
- all content (folders + 47 letters, one paint) — *is approximately* — 1015 ms
- .folders-container.loaded — *is approximately* — 1289 ms
- load event — *is approximately* — 1601 ms
- Server-streamed single paint — *is a finding for* — eArchive full reload
- Server-streamed single paint — *implies* — first 48 items
- first 48 items — *is equivalent to* — all items
- first 48 items — *is equivalent on* — build
- load event — *is described as* — bimodal
- load event — *mostly occurs at* — ~1.5–1.7 s
- load event — *occasionally occurs at* — ~3.2 s
- load event — *occasionally occurs due to* — late sub-resource
- Content — *is usable at* — ~1 s
- severe hiccup — *affects* — ~20 % of reloads
- severe hiccup — *observed on* — 800k tenant
- severe hiccup — *is a signal for* — backend/latency reliability signal
- backend/latency reliability signal — *is separate from* — render path
- outlier — *blows* — max/spread
- median — *is preferred over* — mean
- Perf 800k tenant eArchive reload timing — *related to* — Measure component render timing with Playwright addInitScript
- Perf 800k tenant eArchive reload timing — *related to* — Reload not automatically faster than first load

%% ai-graph-end %%