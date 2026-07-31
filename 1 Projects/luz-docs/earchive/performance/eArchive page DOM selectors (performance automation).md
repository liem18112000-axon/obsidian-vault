---
ai_hash: 6a0b4a03878d528b
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-16
entities:
- eArchive page
- DOM selectors
- performance automation
- Playwright
- a11y snapshot
- getByText
- doc list
- Manage access rights marker
- wait_for
- browser_evaluate
- DOM
- Doc items
- .ui-datascroller-item
- PrimeFaces DataScroller
- .letter-wrapper
- .letter-content
- .brand-container
- Search box
- input.ui-inputtext[placeholder="Document name, sender name or body content"]
- Badges
- body.innerText
- Custom (\d+) regex
- Documents (\d+) regex
- New status
- Folder rows
- Garden-1 / 0 Files
- Books-6
- Computers-2
- Load-complete detection
- document.querySelectorAll('.ui-datascroller-item').length
- Lazy load
- window scroll to bottom
- window.scrollTo(0, document.body.scrollHeight)
- Documents (800000) total
- 800k perf tenant
- Luz performance env cluster topology
tags:
- luz
- earchive
- playwright
- primefaces
- automation
title: eArchive page DOM selectors (performance automation)
---

# eArchive page DOM selectors (performance automation)

Driving the eArchive page (`.../ch.klara.epostbusiness.LetterStorage/LetterStorage.xhtml`) with Playwright: **the a11y snapshot / `getByText` does NOT reliably surface the doc list or the "Manage access rights" marker** on performance — `wait_for {text}` times out even on a fully-rendered page. Use `browser_evaluate` + DOM instead.

## Stable selectors
- **Doc items**: `.ui-datascroller-item` (PrimeFaces **DataScroller**, lazy). Also 1:1 with `.letter-wrapper`, `.letter-content`, `.brand-container`. Count = rendered doc count.
- **Search box**: `input.ui-inputtext[placeholder="Document name, sender name or body content"]`.
- **Badges** (regex on `body.innerText`): `Custom\s*\((\d+)\)`, `Documents\s*\((\d+)\)`.
- **"New" status**: count `.ui-datascroller-item` whose text contains `New`.
- **Folder rows**: text lines `<folder> \n <N> Files` (e.g. `Garden-1 / 0 Files`). Folder names also appear inline as `Archived in Books-6, Computers-2, …`.

## Load-complete detection (replaces wait_for)
Poll `browser_evaluate` until `document.querySelectorAll('.ui-datascroller-item').length > 0` AND `body.innerText.includes('Manage access rights')`.

## Lazy load ("load more 24")
DataScroller loads the next chunk on **window scroll to bottom**: `window.scrollTo(0, document.body.scrollHeight)`, then poll until `.ui-datascroller-item` count increases. Page reports `Documents (800000)` total on the 800k perf tenant; initial render observed = 47 items.

Related: [[Luz performance env cluster topology]]

%% ai-graph-start %%

**Related notes:**
- [[Measure component render timing with Playwright addInitScript]]
- [[eArchive perf test plan 5 scenarios, all automated by trace tool]]
- [[Trace tool folder-drill waits 3min because folder view lacks Documents-Custom counters]]
- [[eArchive request flow and log correlation (perf)]]
- [[Resettable MutationObserver harness measures skeleton-to-number appear time on SSR pages]]

**Relations:**
- eArchive page — *is for* — performance automation
- eArchive page — *driven by* — Playwright
- a11y snapshot — *does not reliably surface* — doc list
- a11y snapshot — *does not reliably surface* — Manage access rights marker
- getByText — *does not reliably surface* — doc list
- getByText — *does not reliably surface* — Manage access rights marker
- wait_for — *times out* — on performance
- browser_evaluate — *replaces* — wait_for
- DOM — *replaces* — wait_for
- Doc items — *use selector* — .ui-datascroller-item
- .ui-datascroller-item — *is part of* — PrimeFaces DataScroller
- Doc items — *is 1:1 with* — .letter-wrapper
- Doc items — *is 1:1 with* — .letter-content
- Doc items — *is 1:1 with* — .brand-container
- Search box — *uses selector* — input.ui-inputtext[placeholder="Document name, sender name or body content"]
- Badges — *use regex on* — body.innerText
- Custom (\d+) regex — *is for* — Badges
- Documents (\d+) regex — *is for* — Badges
- New status — *counts* — .ui-datascroller-item
- Load-complete detection — *polls* — browser_evaluate
- Load-complete detection — *checks* — document.querySelectorAll('.ui-datascroller-item').length
- Load-complete detection — *checks* — body.innerText.includes('Manage access rights')
- Lazy load — *is a feature of* — DataScroller
- Lazy load — *triggers on* — window scroll to bottom
- window scroll to bottom — *is implemented by* — window.scrollTo(0, document.body.scrollHeight)
- Lazy load — *polls for* — .ui-datascroller-item count increase
- Documents (800000) total — *reported on* — 800k perf tenant
- Luz performance env cluster topology — *is related to* — eArchive page

%% ai-graph-end %%