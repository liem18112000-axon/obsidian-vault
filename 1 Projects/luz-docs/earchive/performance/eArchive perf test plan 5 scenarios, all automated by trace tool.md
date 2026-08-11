---
ai_hash: f1af1909dbd24571
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
aliases:
- 'eArchive perf test plan: 5 scenarios, trace tool automates only scenario 1'
created: 2026-07-21
entities:
- eArchive perf test plan
- 5 scenarios
- trace tool
- luz_docs/docs/performance-test-800k/end-to-end-tools
- eArchive page
- scroll to load more items
- open file-details popup
- choose a non-root/non-Trash folder
- scroll-load-more inside that folder
- Playwright trace tool
- trace-earchive.js
- browser session
- report.html
- in-page recorder
- window.__rec
- MutationObserver
- item counts
- folder rows
- K Files
- Documents (N)
- Custom (M)
- first-appearance times
- window.__resetRec()
- folder click
- folder-drill times
- addInitScript
- full navigations
- Scroll batches
- window.__scrollMark
- item count stability
- visible dialog/overlay/pdf iframe
- first item
- Output dir
- env
- URL
- dev.klara.tech
- dev
- performance.klara.tech
- perf
- hostname label
- eArchive counter metrics timed from page-load start to skeleton replacement
source: session 2026-07-21
status: seedling
tags:
- earchive
- performance
- playwright
- test-plan
title: 'eArchive perf test plan: 5 scenarios, all automated by trace tool'
type: observation
---

# eArchive perf test plan: 5 scenarios, all automated by trace tool

The eArchive end-to-end performance test plan (luz_docs/docs/performance-test-800k/end-to-end-tools) defines 5 scenarios: (1) enter eArchive page, (2) scroll to load more items, (3) open file-details popup, (4) choose a non-root/non-Trash folder, (5) scroll-load-more inside that folder. The Playwright trace tool (trace-earchive.js) now automates **all five** in one browser session, reported as "test.md — N" sections in report.html.

Mechanics worth remembering:
- Scenarios 1 & 4 use an in-page recorder `window.__rec` (MutationObserver tick scans item counts, folder rows, and regex-parses "K Files" / "Documents (N)" / "Custom (M)" from rendered text, stamping first-appearance times). `window.__resetRec()` re-arms it before the folder click so folder-drill times are relative to that click; addInitScript re-injection makes this survive full navigations.
- Scroll batches (2 & 5) use `window.__scrollMark`: batch considered complete when the item count is stable for 2.5 s.
- Scenario 3 waits for any visible dialog/overlay/pdf iframe after clicking the first item.
- Output dir is `out-<env>-<timestamp>/`, env derived from URL (dev.klara.tech → dev, performance.klara.tech → perf, else first hostname label).

## Related

- [[eArchive counter metrics timed from page-load start to skeleton replacement]]

%% ai-graph-start %%

**Related notes:**
- [[Perf 800k tenant eArchive reload timing]]
- [[Trace tool folder-drill waits 3min because folder view lacks Documents-Custom counters]]
- [[eArchive page DOM selectors (performance automation)]]
- [[Resettable MutationObserver harness measures skeleton-to-number appear time on SSR pages]]
- [[Dev eArchive baseline items in 6s but count badges take 22-41s]]

**Relations:**
- eArchive perf test plan — *defines* — 5 scenarios
- eArchive perf test plan — *documented_at* — luz_docs/docs/performance-test-800k/end-to-end-tools
- 5 scenarios — *automated_by* — trace tool
- trace tool — *is_a* — Playwright trace tool
- Playwright trace tool — *implemented_by* — trace-earchive.js
- trace-earchive.js — *automates* — 5 scenarios
- 5 scenarios — *executed_in* — browser session
- browser session — *generates* — report.html
- report.html — *contains_sections_for* — 5 scenarios
- 5 scenarios — *include* — eArchive page
- 5 scenarios — *include* — scroll to load more items
- 5 scenarios — *include* — open file-details popup
- 5 scenarios — *include* — choose a non-root/non-Trash folder
- 5 scenarios — *include* — scroll-load-more inside that folder
- eArchive page — *uses* — in-page recorder
- choose a non-root/non-Trash folder — *uses* — in-page recorder
- in-page recorder — *is* — window.__rec
- window.__rec — *uses* — MutationObserver
- MutationObserver — *scans* — item counts
- MutationObserver — *scans* — folder rows
- MutationObserver — *parses* — K Files
- MutationObserver — *parses* — Documents (N)
- MutationObserver — *parses* — Custom (M)
- window.__rec — *stamps* — first-appearance times
- window.__resetRec() — *re-arms* — window.__rec
- window.__resetRec() — *used_before* — folder click
- folder-drill times — *relative_to* — folder click
- addInitScript — *ensures_survival_of* — window.__rec
- addInitScript — *ensures_survival_across* — full navigations
- Scroll batches — *are* — scroll to load more items
- Scroll batches — *are* — scroll-load-more inside that folder
- Scroll batches — *use* — window.__scrollMark
- window.__scrollMark — *completes_on* — item count stability
- open file-details popup — *waits_for* — visible dialog/overlay/pdf iframe
- visible dialog/overlay/pdf iframe — *appears_after_clicking* — first item
- Output dir — *format_is* — out-<env>-<timestamp>/
- env — *derived_from* — URL
- URL — *dev.klara.tech maps_to_env* — dev
- URL — *performance.klara.tech maps_to_env* — perf
- URL — *uses_for_env* — first hostname label
- eArchive perf test plan — *related_to* — eArchive counter metrics timed from page-load start to skeleton replacement

%% ai-graph-end %%