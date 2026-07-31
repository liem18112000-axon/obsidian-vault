---
title: "Verify Facebook comment sort switch by re-reading the sort button label"
created: 2026-06-11
type: lesson
status: seedling
source: "fb-info-project session 2026-06-11"
tags: [facebook, playwright, scraping, gotcha]
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
