---
ai_hash: 2432346d0f975f7b
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-22
entities: []
source: fb-info-project session 2026-06-22
status: seedling
tags:
- facebook
- scraping
- dom
- playwright
- gotcha
title: Facebook reply hierarchy lives in the article aria-label, not DOM nesting
type: lesson
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

%% ai-graph-start %%

**Related notes:**
- [[Facebook ships comment aria-labels in English even when the UI is Vietnamese]]
- [[Facebook reply-expander button label variants]]
- [[Unanchored 'From' regex captures the profile name from Facebook's 'See more from' buttons]]
- [[Facebook reel comments are hidden behind the comment icon]]
- [[Facebook Comet comment DOM does not expose the commenter's numeric UID]]

%% ai-graph-end %%