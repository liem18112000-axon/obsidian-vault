---
ai_hash: 6f97cf2ebfaf54d1
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-21
entities: []
source: session 2026-07-21
status: seedling
tags:
- playwright
- performance
- earchive
- mutationobserver
- luz-docs
title: Resettable MutationObserver harness measures skeleton-to-number appear time
  on SSR pages
type: howto
---

# Resettable MutationObserver harness measures skeleton-to-number appear time on SSR pages

A page that streams SSR content (klara eArchive, JSF) shows skeletons/spinners that later become numbers ("Documents (N)", per-folder "K Files"). To time skeleton→number reliably: inject a MutationObserver harness via Playwright `addInitScript` (survives full navigations because it re-runs at document-start), and detect the values with **text regexes on `textContent`** (`/Documents\s*\((\d+)\)/`, `/(\d+\+?)\s*Files/`) instead of guessing CSS selectors — `textContent` does not force layout, so scanning on every (throttled) mutation stays cheap.

Key trick: make the record **resettable** (`window.__resetRec()` re-stamps `t0` and clears captured values). Call it right before an in-app click (folder drill) so all times are relative to the click — and it is correct in *both* worlds: if the click is a full navigation, `addInitScript` re-arms a fresh harness anyway; if it is an AJAX partial update, the manual reset does the job.

Used in `luz_docs/docs/performance-test-800k/end-to-end-tools/trace-earchive.js` (extended 2026-07-21 with the 5 test.md scenarios: enter page, scroll load-more, view details popup, folder choose, scroll in folder).

## Related

- [[eArchive front-end trace tool]]

%% ai-graph-start %%

**Related notes:**
- [[Measure component render timing with Playwright addInitScript]]
- [[eArchive perf test plan 5 scenarios, all automated by trace tool]]
- [[eArchive page DOM selectors (performance automation)]]
- [[eArchive counter metrics timed from page-load start to skeleton replacement]]
- [[eArchive count skeleton CSS classes are the reliable counts-loaded signal]]

%% ai-graph-end %%