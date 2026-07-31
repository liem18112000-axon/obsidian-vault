---
title: "Facebook's Newest sort option mentions 'all comments' in its description — anchor the label regex"
created: 2026-06-11
type: lesson
status: evergreen
source: "fb-info-project live debug 2026-06-11"
tags: [facebook, playwright, regex, gotcha]
---

# Facebook's Newest sort option mentions 'all comments' in its description — anchor the label regex

Facebook's comment-sort dropdown items carry a title plus a description, and the **Newest** item's description is "Show **all comments** with the newest comments first." — so an unanchored regex like `/All comments|Tất cả bình luận/i` matches the *Newest* menu item first (it precedes All comments in DOM order). A Playwright `.filter(has_text=...).first` then silently switches the sort to Newest instead of All comments.

Fix: anchor the pattern to the start of the element text — `/^\s*(All comments|Tất cả bình luận)/i` — which only matches the item whose *label* leads with the target words (innerText of a menu item starts with its title). Verified against production DOM in fb-info-project, 2026-06-11.

## Related

- [[Verify Facebook comment sort switch by re-reading the sort button label]]
- [[Build test fakes from verbatim production data]]
- [[decoys included]]
