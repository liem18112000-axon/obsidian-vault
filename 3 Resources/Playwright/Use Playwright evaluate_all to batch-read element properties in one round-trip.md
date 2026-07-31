---
title: "Use Playwright evaluate_all to batch-read element properties in one round-trip"
created: 2026-06-15
type: howto
status: seedling
source: "fb-info-project Phase 2 / B2, 2026-06-15"
tags: [playwright, scraping, performance, dom]
---

# Use Playwright evaluate_all to batch-read element properties in one round-trip

When you need many properties off many elements, read them in **one** in-page call with `locator.evaluate_all(js)` instead of looping Python-side with per-element `get_attribute`/`inner_text` calls — each of those is a separate round-trip to the browser.

```python
JS = "els => els.map(e => ({href: e.getAttribute('href') || '', text: (e.textContent || '').trim()}))"
rows = page.locator('a').evaluate_all(JS)   # list[dict], one round-trip total
```

**Why it matters:**
- **Speed:** N elements × M props collapses from N×M round-trips to 1. On a heavy DOM (hundreds of articles, thousands of anchors) this is the difference between minutes and seconds.
- **No timeouts:** `inner_text` triggers a layout/visibility wait and accepts a `timeout=`; on a heavy or re-rendering DOM it blocks and can raise `Locator.inner_text: Timeout exceeded`. `textContent` (used inside evaluate_all) is layout-free — it never blocks or times out.

**Trade-off:** `textContent` returns raw concatenated text of all descendants (incl. hidden nodes), whereas `inner_text` returns rendered visible text. For matching names/timestamps/ids it's fine after `.trim()`; if you specifically need *visible* text, evaluate_all with `e.innerText` still beats per-element round-trips.

Concrete use: fb-info-project's comment harvest — see `docs/phase2-grouppost-scan-progress.md` and [[Large FB group posts expand huge but article-scan yields almost no profiles]].

## Related

- [[Large FB group posts expand huge but article-scan yields almost no profiles]]
