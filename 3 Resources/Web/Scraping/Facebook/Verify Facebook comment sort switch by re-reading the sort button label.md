---
ai_hash: 13c641ae9da0849a
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-11
entities: []
source: fb-info-project session 2026-06-11
status: seedling
tags:
- facebook
- playwright
- scraping
- gotcha
title: Verify Facebook comment sort switch by re-reading the sort button label
type: lesson
---

# Verify Facebook comment sort switch by re-reading the sort button label

Clicking Facebook's comment-sort control "blind" (e.g. Playwright `page.get_by_text(rx).click()` inside a swallowed try/except) fails silently and leaves the post on **Most relevant**, which hides the majority of comments. The reliable shape is a verified retry loop:

1. Locate the sort chip as `div[role="button"]` whose text matches the *current* label ("Most relevant" / "Top comments" / "Newest" / "Phù hợp nhất" / "Mới nhất" ...).
2. Click it, then click the `div[role="menuitem"]` matching "All comments" / "Tất cả bình luận" inside the menu that opens.
3. **Verify** by re-checking that a `div[role="button"]` now carries the All-comments label — the button text changes only when the switch really happened.
4. Retry a few times; only warn after the last attempt fails (tiny posts may have no sort control at all).

Targeting `role=menuitem` matters: a bare text locator can hit a hidden or unrelated node, and menu items are never `role=button`, so the verification check can't false-positive while the menu is open.

## Related

- [[Switch Facebook comment sort to All comments before any scrolling or expansion]]

%% ai-graph-start %%

**Related notes:**
- [[Facebook's Newest sort option mentions 'all comments' in its description — anchor the label regex]]
- [[Switch Facebook comment sort to All comments before any scrolling or expansion]]
- [[Facebook post permalinks render the post twice — dialog plus a hidden page copy]]
- [[Self-healing scraper selectors — LLM fallback only on verified failure, then cache]]
- [[Facebook reel comments are hidden behind the comment icon]]

%% ai-graph-end %%