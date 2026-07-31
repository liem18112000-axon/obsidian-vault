---
ai_hash: 30b9c37b2bd1f713
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-11
entities: []
source: fb-info-project live debug 2026-06-11
status: evergreen
tags:
- facebook
- playwright
- regex
- gotcha
title: Facebook's Newest sort option mentions 'all comments' in its description —
  anchor the label regex
type: lesson
---

# Facebook's Newest sort option mentions 'all comments' in its description — anchor the label regex

Facebook's comment-sort dropdown items carry a title plus a description, and the **Newest** item's description is "Show **all comments** with the newest comments first." — so an unanchored regex like `/All comments|Tất cả bình luận/i` matches the *Newest* menu item first (it precedes All comments in DOM order). A Playwright `.filter(has_text=...).first` then silently switches the sort to Newest instead of All comments.

Fix: anchor the pattern to the start of the element text — `/^\s*(All comments|Tất cả bình luận)/i` — which only matches the item whose *label* leads with the target words (innerText of a menu item starts with its title). Verified against production DOM in fb-info-project, 2026-06-11.

## Related

- [[Verify Facebook comment sort switch by re-reading the sort button label]]
- [[3 Resources/Practices/Testing/Build test fakes from verbatim production data, decoys included]]

%% ai-graph-start %%

**Related notes:**
- [[Verify Facebook comment sort switch by re-reading the sort button label]]
- [[Build test fakes from verbatim production data, decoys included]]
- [[Facebook post permalinks render the post twice — dialog plus a hidden page copy]]
- [[Switch Facebook comment sort to All comments before any scrolling or expansion]]
- [[LLM-picked UI actions can be verified mechanically but not semantically]]

%% ai-graph-end %%