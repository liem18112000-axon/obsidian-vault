---
tags: [playwright, mcp, performance, technique, klara]
created: 2026-07-18
---

# Measure component render timing with Playwright addInitScript

Goal: time *when individual components appear* on a full page load (first N items, folder list, spinner gone) with sub-ms precision — not just whole-page wall clock.

## Why not inject-before-click
Anchoring `window.__t0` before a click only survives an **SPA in-app render**. If the click triggers a **full navigation** (URL changes, new document), injected JS is wiped → `window.__t0` reads `null` afterward. KLARA eArchive is the full-nav case (click → `LetterStorage.xhtml`).

## Technique: `page.addInitScript` (persists across navigations, runs at document-start)
Install via `browser_run_code_unsafe` with an `async (page) => {...}` wrapper (top-level `page` is a syntax error — must be the function arg). The init script:
- sets `window.__t0 = performance.now()` (≈ navigation start, since `performance.now()` is relative to `timeOrigin`)
- registers a `MutationObserver` on `document.documentElement` with `{childList, subtree, attributes, attributeFilter:['class','style']}`
- each callback stamps `window.__marks[name] = round(now - t0)` the first time a milestone predicate flips true

Milestone predicate types used: `count` (`querySelectorAll(sel).length >= n`), `present`, `goneVis` (was visible via `offsetParent!==null`, then gone — the seen-first flag prevents firing before the spinner ever mounts).

Then `browser_navigate` to reload, `wait_for` the render marker, and read back:
```js
() => ({ marks: window.__marks, elapsed: Math.round(performance.now()-window.__t0) })
```
Also pull `performance.getEntriesByType('navigation')` + `'paint'` for FCP / domInteractive / responseEnd / loadEvent.

## Key finding: KLARA eArchive renders server-side (streamed), not progressively
All content milestones (first folder row, all 4 folders, first letter, 24 letters, all 47 letters, 48 datascroller items) stamped the **same** timestamp (~1181ms) — the observer's first callback saw everything at once. The HTML is server-rendered and streamed; the browser paints `.letter-wrapper`s as bytes stream in (content mark 1181ms < `responseEnd` 1533ms is consistent with chunked transfer). So "time for first 48 items" ≈ "time for all items" here — there is no client-side incremental fill to measure. The only separately-timed step is the JS-added `.folders-container.loaded` class (~1407ms).

## eArchive selectors (performance.klara.tech, 2026-07)
- doc cards: `.letter-wrapper` (47) / list items `.ui-datascroller-item` (52 incl. folders+loader)
- folder rows: `.folder-content` (4); folder list done = `.folders-container.loaded`
- global AJAX spinner: `.ajax-loading-status-container` (hidden on full nav — never fired)
- post-load marker text: `Manage access rights` (headings `Documents`/`Custom` are a11y-hidden)

## Full-nav vs AJAX partial: rearm for in-app actions
`addInitScript` only re-fires on a **full navigation**. An in-app action that does an **AJAX partial update** (same document — e.g. PrimeFaces folder filter) does NOT re-trigger it, so `window.__marks` goes stale. Before such an action, call a `REARM` via `browser_evaluate` that disconnects the old observer and re-runs the init body (resets `t0`/`marks`, reconnects). Safe both ways: if the action turns out to be a full nav, the rearmed state is wiped and `addInitScript` re-arms the new document.

**AJAX caveat:** on a partial update the OLD items are still in the DOM when the observer restarts, so count milestones stamp ~0 ms against stale content. Trust `spinnerGone` (global AJAX spinner appear→gone, via the was-visible-then-gone flag) as the real load time; treat count marks as informational.

**Observed on klara (2026-07):** eArchive folder-open is actually a FULL navigation (`LetterStorage.xhtml` → `LetterStorageDetail.xhtml`, new instance id), NOT AJAX. So addInitScript re-fires, content marks are valid, and `spinnerGone` stays blank (no AJAX spinner on full nav). The rearm/AJAX path above is defensive-only for this build. Also: the Detail page has no `Manage access rights` marker — waiting on it times out; wait on the folder name instead. Confirmed drill (Music-0): FCP 1476ms, all content 2221ms, foldersLoaded 2499ms, responseEnd 2787ms.

Related: [[Playwright browser_wait_for time is a hard sleep]]
