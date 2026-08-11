---
ai_hash: cfbbd2cbc1003f20
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-30
entities: []
source: fb-info-project thorough-mode live run, session 2026-06-30
status: seedling
tags:
- playwright
- performance
- dom
- gotcha
- facebook
title: innerText forces layout and can hang Playwright scans on huge DOMs; prefer
  textContent
type: lesson
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

%% ai-graph-start %%

**Related notes:**
- [[Playwright click() auto-waits the full timeout on a missing locator; probe with count() first]]
- [[Use Playwright evaluate_all to batch-read element properties in one round-trip]]
- [[Large FB group posts expand huge but article-scan yields almost no profiles]]
- [[Distinguish absent control from missed click when expanding lazy lists]]
- [[Facebook's Newest sort option mentions 'all comments' in its description — anchor the label regex]]

%% ai-graph-end %%