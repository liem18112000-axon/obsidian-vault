---
title: "innerText forces layout and can hang Playwright scans on huge DOMs; prefer textContent"
created: 2026-06-30
type: lesson
status: seedling
source: "fb-info-project thorough-mode live run, session 2026-06-30"
tags: [playwright, performance, dom, gotcha, facebook]
---

# innerText forces layout and can hang Playwright scans on huge DOMs; prefer textContent

In a page-side scan run via Playwright `page.evaluate`, reading **`el.innerText`** forces a synchronous layout/reflow for each element, because innerText reflects *rendered* (CSS-aware, visibility-aware) text. Do that across a large `querySelectorAll('h1,h2,h3,h4,h5,span,strong')` match set — especially every loop iteration — and on a big DOM it degrades from slow to an effective **hang** (minutes per call).

**`el.textContent` is layout-free**: it returns the raw concatenated text without triggering reflow, so it never blocks the same way. Prefer it for bulk scans where you don't strictly need visibility/CSS-rendered text.

**Real case (fb-info-project):** the same post was double-rendered ("an unclickable duplicate sort chip remains"), giving a giant DOM. `collector.COMMENT_TREE_JS` already uses `textContent` and documents why. But `browser.SCROLL_POST_SCOPED_JS` and `browser.TAG_REGION_JS` still scan with `el.innerText || el.textContent` (innerText first) over h1–h5/span/strong **every expand iteration** — which froze the comment-expansion phase for 7+ min in both headless and visible runs, right after the sort switch. Fix direction: drop innerText-first to `textContent` in those two snippets (the marker text is short, so the visibility/whitespace difference rarely matters), or guard/cache the scan so it isn't re-run every round.

Tradeoff: innerText respects visibility (hidden nodes return "") and normalizes whitespace; textContent does neither. Only swap when the scan can tolerate that — for short marker/heading detection it usually can.

## Related
[[Distinguish absent control from missed click when expanding lazy lists]]
[[fb-info-project]]

## Related

- [[Distinguish absent control from missed click when expanding lazy lists]]
