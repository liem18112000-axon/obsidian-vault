---
ai_hash: c9f5746ce6ed1eea
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-16
entities: []
source: session 2026-07-16
status: seedling
tags:
- primefaces
- jsf
- playwright
- timing
- gotcha
- luz-docs
title: 'Timing PrimeFaces dialog opens: trusted click + stale-guard the reused dialog
  node'
type: lesson
---

# Timing PrimeFaces dialog opens: trusted click + stale-guard the reused dialog node

When timing "open a document / open a dialog" in a PrimeFaces (JSF) app via a headless browser, two gotchas make naive timing wrong:

1. **`element.click()` may not fire the handler.** PrimeFaces click behaviour is often bound via delegated jQuery listeners, not an inline `onclick`, so a synthetic `element.click()` silently does nothing. Use a **trusted click** (Playwright `browser_click` on a selector/ref) which dispatches real mouse events. (If the element genuinely has an inline `onclick="..."` attribute, `element.click()` does fire it — inspect first.)

2. **PrimeFaces reuses ONE dialog DOM node**, swapping its content via AJAX rather than creating a new node per open. Consequences:
   - "new dialog appeared" detection by counting `.ui-dialog` nodes never fires (count stays 1).
   - Reopening shows the PREVIOUS documents content instantly, so a "dialog visible + has content" check resolves in ~150 ms — a false-fast reading, not the real load.
   - Fix: **stale-guard** — read the new records id from the trigger (e.g. the `onClickViewLetter_...(<oid>)` onclick arg) and wait until that id appears in the dialogs innerHTML before stopping the timer. Also close + wait until the dialog is hidden between opens.

Also: seed/synthetic documents with no real binary render a `document-viewer-unsupported__container` placeholder (no iframe/img/canvas), so a viewer-media detector will time out even though the dialog opened — detect the container or a content-length threshold, not just media elements.

Context: luz-docs 800k eArchive "open a document" ×10 timing (client ≈1.2–1.8 s, one 7.8 s outlier), journey-report.html J3.

## Related
[[luz-docs 800k eArchive performance test]]
[[Reconstruct request waterfall from undertow accesslog start = timestamp minus time-consuming]]

## Related

- [[luz-docs 800k eArchive performance test]]

%% ai-graph-start %%

**Related notes:**
- [[Measure component render timing with Playwright addInitScript]]
- [[eArchive page DOM selectors (performance automation)]]
- [[Trace tool folder-drill waits 3min because folder view lacks Documents-Custom counters]]
- [[Server-rendered JSF apps never expose their internal API calls to browser network capture]]
- [[Perf 800k tenant eArchive reload timing]]

%% ai-graph-end %%