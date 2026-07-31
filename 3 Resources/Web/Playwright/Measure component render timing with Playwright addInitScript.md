---
title: "Measure component render timing with Playwright addInitScript"
created: 2026-07-18
type: technique
status: seedling
source: "session 2026-07-18, KLARA eArchive perf"
tags: [playwright, mcp, performance, technique, klara]
---

# Measure component render timing with Playwright addInitScript

To time *when individual components appear* on a page load with sub-ms precision (not whole-page wall clock), install the timer with **`page.addInitScript`** — it runs at document-start and survives navigations. Anchoring `window.__t0` before a click only works for an SPA in-app render; a full navigation wipes injected JS and `__t0` reads `null`.

The init script:
- sets `window.__t0 = performance.now()` (≈ navigation start, since `performance.now()` is relative to `timeOrigin`);
- registers a `MutationObserver` on `document.documentElement` with `{childList, subtree, attributes, attributeFilter:['class','style']}`;
- stamps `window.__marks[name] = round(now - t0)` the first time each milestone predicate flips true.

Milestone predicate types: `count` (`querySelectorAll(sel).length >= n`), `present`, `goneVis` (was visible via `offsetParent !== null`, then gone — a seen-first flag prevents firing before a spinner ever mounts).

Install via `browser_run_code_unsafe` with an `async (page) => {…}` wrapper (top-level `page` is a syntax error — it must be the function arg). Then `browser_navigate` to reload, `wait_for` a render marker, and read back:
```js
() => ({ marks: window.__marks, elapsed: Math.round(performance.now()-window.__t0) })
```
Also pull `performance.getEntriesByType('navigation')` + `'paint'` for FCP / domInteractive / responseEnd / loadEvent.

**Rearm for AJAX partials.** `addInitScript` only re-fires on a full navigation. Before an in-app action that does a same-document partial update (e.g. a PrimeFaces filter), call a REARM via `browser_evaluate` that disconnects the old observer and re-runs the init body (resets `t0`/`marks`, reconnects). Safe either way: if it turns out to be a full nav, the rearmed state is wiped and `addInitScript` re-arms. Caveat: on a partial update the OLD items are still in the DOM when the observer restarts, so count milestones stamp ~0ms against stale content — trust `spinnerGone` (appear→gone flag) as the real load time and treat counts as informational.

**Finding — a server-rendered page has nothing to measure progressively.** On KLARA eArchive every content milestone (first folder row, all 4 folders, first letter, 24 letters, all 47 letters, 48 datascroller items) stamped the SAME ~1181ms: the HTML is server-rendered and streamed, so the browser paints as bytes arrive (content mark 1181ms < `responseEnd` 1533ms, consistent with chunked transfer). "Time for first 48 items" ≈ "time for all items". Only the JS-added `.folders-container.loaded` class timed separately (~1407ms).

**eArchive selectors (performance.klara.tech, 2026-07):** doc cards `.letter-wrapper` (47); list items `.ui-datascroller-item` (52 incl. folders+loader); folder rows `.folder-content` (4); folder list done `.folders-container.loaded`; global AJAX spinner `.ajax-loading-status-container` (hidden on full nav — never fires); post-load marker text `Manage access rights` (the `Documents`/`Custom` headings are a11y-hidden). Folder-open is a FULL navigation (`LetterStorage.xhtml` → `LetterStorageDetail.xhtml`), not AJAX, and the Detail page has no `Manage access rights` marker — wait on the folder name instead. Confirmed drill (Music-0): FCP 1476ms, all content 2221ms, foldersLoaded 2499ms, responseEnd 2787ms.

## Related

- [[Playwright browser_wait_for time is a hard sleep]]
- [[3 Resources/Web/Playwright/Playwright full-nav detection needs a JS-heap marker not URL compare]]
