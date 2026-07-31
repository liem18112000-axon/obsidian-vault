---
title: eArchive page DOM selectors (performance automation)
tags: [luz, earchive, playwright, primefaces, automation]
created: 2026-07-16
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
