---
ai_hash: a5a9ac72201b56a8
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
aliases:
- Playwright URL compare before-after click detects SPA vs full navigation
created: 2026-07-21
entities: []
source: session 2026-07-21
status: seedling
tags:
- playwright
- technique
- spa
- gotcha
title: Playwright full-nav detection needs a JS-heap marker, not URL compare
type: howto
---

# Playwright full-nav detection needs a JS-heap marker, not URL compare

Comparing `page.url()` before/after a click does NOT distinguish a full page navigation from an SPA route change — `page.url()` reflects `history.pushState` too, so an in-place SPA route change looks identical to a real navigation (confirmed empirically: eArchive folder click changed the URL, gated wait still burned its 90 s timeout).

The reliable signal is the JS heap: a full navigation wipes `window`, an SPA update keeps it. Plant a marker before the click and check for its absence after:

```js
await page.evaluate(() => { window.__preClick = true; });
await locator.click();
await page.waitForTimeout(1500);
const fullNav = await page.evaluate(() => !window.__preClick).catch(() => true);
if (fullNav) {
  // real navigation: safe to wait for nav-only landmarks
}
```

The `.catch(() => true)` treats an evaluate failure mid-navigation as a full nav. Used in the eArchive trace tool to gate the 90 s "Manage access rights" wait on folder clicks.

## Related

- [[Trace tool folder-drill waits 3min because folder view lacks Documents-Custom counters]]

%% ai-graph-start %%

**Related notes:**
- [[Trace tool folder-drill waits 3min because folder view lacks Documents-Custom counters]]
- [[Measure component render timing with Playwright addInitScript]]
- [[eArchive page DOM selectors (performance automation)]]
- [[Timing PrimeFaces dialog opens trusted click + stale-guard the reused dialog node]]
- [[Playwright click() auto-waits the full timeout on a missing locator; probe with count() first]]

%% ai-graph-end %%