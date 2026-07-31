---
title: "Facebook reply hierarchy lives in the article aria-label, not DOM nesting"
created: 2026-06-22
type: lesson
status: seedling
source: "fb-info-project session 2026-06-22"
tags: [facebook, scraping, dom, playwright, gotcha]
---

# Facebook reply hierarchy lives in the article aria-label, not DOM nesting

Facebook does **not** nest a reply's \`div[role="article"]\` inside the article of the comment it answers. Every comment and every reply is a **flat sibling** at the same DOM depth (verified live on one post: 260 comments + 550 replies, all depth-1 under the post container). So any approach based on DOM nesting / nearest-ancestor-article cannot recover the reply hierarchy — it yields "parent = none" for everything.

The reliable signal is each article's **`aria-label`**, which names the relationship outright:
- `Comment by X <time>` → top-level comment
- `Reply by X to Y's comment <time>` → reply to a comment (parent = Y)
- `Reply by X to Y's reply <time>` → reply to a reply (parent = Y)

**How to resolve the parent profile URL:** the aria-label gives the parent's display name only. Match it to the most recent **preceding** article by that name — Facebook renders a thread parent before its replies, so nearest-preceding-by-name is correct.

This was the root cause of the empty "Trả lời ai" (reply-to) column in the fb-info-project scraper. Fix lived in `collector.py` (`COMMENT_TREE_JS` now returns `aria-label` instead of an ancestor index; new `reply_parent()`) and `patterns.py` (`ARIA_REPLY_EN/VI/IS_REPLY`).

See also the locale gotcha: [[Facebook ships comment aria-labels in English even when the UI is Vietnamese]].

## Related

- [[Facebook ships comment aria-labels in English even when the UI is Vietnamese]]
