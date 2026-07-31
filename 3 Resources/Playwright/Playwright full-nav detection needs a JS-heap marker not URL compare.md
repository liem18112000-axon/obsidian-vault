---
title: "Playwright full-nav detection needs a JS-heap marker, not URL compare"
created: 2026-07-21
aliases: ["Playwright URL compare before-after click detects SPA vs full navigation"]
type: howto
status: seedling
source: "session 2026-07-21"
tags: [playwright, technique, spa, gotcha]
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
