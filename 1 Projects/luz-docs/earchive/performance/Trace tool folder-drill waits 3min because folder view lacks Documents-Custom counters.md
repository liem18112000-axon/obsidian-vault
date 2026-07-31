---
ai_hash: 36104c80d7233eac
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-21
entities:
- Trace tool
- folder-drill view
- Documents-Custom counters
- eArchive
- collectPageEnter
- Manage access rights
- scenario 4
- trace-earchive.js
- requireCounts parameter
- K Files
- URL compare
- JS-heap marker
- readRec polling
- full navigation
- Dev eArchive baseline items in 6s but count badges take 22-41s
- Playwright full-nav detection needs a JS-heap marker not URL compare
- folder click
- done-check
- folder view header
- navigation detection
- harness
source: trace run 2026-07-21
status: seedling
tags:
- earchive
- playwright
- gotcha
title: Trace tool folder-drill waits 3min because folder view lacks Documents-Custom
  counters
type: lesson
---

# Trace tool folder-drill waits 3min because folder view lacks Documents-Custom counters

In the eArchive folder-drill view there is no "Documents (N)" / "Custom (M)" counter, and clicking a folder is not a full navigation (no "Manage access rights" re-render). The trace tool's collectPageEnter completion check required both counters, so scenario 4 always burned its full 90 s poll timeout — plus another 90 s waiting for "Manage access rights" — making the folder scenario take ~3 minutes of pure timeout on top of real load time.

**Fixed (2026-07-21)** in `trace-earchive.js`: collectPageEnter gained a `requireCounts` param (passed `false` for folder drills, so the done-check drops the two counters), and the "Manage access rights" wait after the folder click was **removed outright** — the folder view header is back-arrow + folder name + "K Files" and never renders that landmark, so gating the wait on navigation detection (tried URL compare, then a JS-heap marker) was moot: the wait can never succeed there. collectPageEnter's readRec polling alone handles readiness, including a full nav re-injecting the harness. Saves ~3 min per run.

## Related

- [[1 Projects/luz-docs/earchive/performance/Dev eArchive baseline items in 6s but count badges take 22-41s]]
- [[Playwright full-nav detection needs a JS-heap marker not URL compare]]

%% ai-graph-start %%

**Related notes:**
- [[eArchive perf test plan 5 scenarios, all automated by trace tool]]
- [[eArchive company-root and Trash tiles never render K Files badges]]
- [[eArchive counter metrics timed from page-load start to skeleton replacement]]
- [[Playwright full-nav detection needs a JS-heap marker not URL compare]]
- [[Dev eArchive baseline items in 6s but count badges take 22-41s]]

**Relations:**
- Trace tool — *PERFORMS* — folder-drill view
- folder-drill view — *LACKS* — Documents-Custom counters
- folder-drill view — *IS_PART_OF* — eArchive
- collectPageEnter — *REQUIRED* — Documents-Custom counters
- folder-drill view — *CAUSES_DELAY_FOR* — Trace tool
- scenario 4 — *IS_A* — folder-drill view
- scenario 4 — *EXPERIENCED_TIMEOUT* — 90 s poll timeout
- scenario 4 — *EXPERIENCED_TIMEOUT* — 90 s waiting for Manage access rights
- trace-earchive.js — *CONTAINS_FIX_FOR* — Trace tool folder-drill view delay
- collectPageEnter — *GAINED_PARAMETER* — requireCounts parameter
- requireCounts parameter — *PASSED_VALUE* — false for folder drills
- done-check — *DROPS* — Documents-Custom counters
- Manage access rights wait — *REMOVED_FROM* — folder-drill view logic
- folder view header — *CONTAINS* — K Files
- folder view header — *DOES_NOT_RENDER* — Manage access rights
- navigation detection — *USED_METHOD* — URL compare
- navigation detection — *USED_METHOD* — JS-heap marker
- readRec polling — *HANDLES* — readiness
- Fix — *SAVES_TIME* — ~3 min per run
- Trace tool folder-drill view delay — *IS_RELATED_TO* — Dev eArchive baseline items in 6s but count badges take 22-41s
- Trace tool folder-drill view delay — *IS_RELATED_TO* — Playwright full-nav detection needs a JS-heap marker not URL compare
- folder click — *IS_NOT_A* — full navigation
- full navigation — *RE_INJECTS* — harness

%% ai-graph-end %%