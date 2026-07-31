---
ai_hash: 72cc5e0c8e7ea03d
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-22
entities: []
source: fb-info-project session 2026-06-22
status: seedling
tags:
- facebook
- scraping
- i18n
- gotcha
title: Facebook ships comment aria-labels in English even when the UI is Vietnamese
type: observation
---

# Facebook ships comment aria-labels in English even when the UI is Vietnamese

When scraping Facebook comments, the comment/reply `aria-label`s come back in **English** ("Comment by …", "Reply by … to …'s comment") **even when the visible UI chrome is Vietnamese** (buttons like "Xem thêm phản hồi", "Trả lời"). The account/page language localizes the visible button text but the article aria-labels stayed English on a live Vietnamese-content post.

**Consequence for parsers:** match aria-labels with English patterns first (confirmed), and treat Vietnamese aria forms ("Phản hồi của X … của Y") as a best-effort fallback — do not assume aria-label locale follows the button-text locale. Always keep a structural fallback that at least marks "this is a reply" even when the parent name can't be parsed.

Surfaced while fixing the reply-to column in fb-info-project. Parent idea: [[Facebook reply hierarchy lives in the article aria-label, not DOM nesting]].

## Related

- [[3 Resources/Web/Scraping/Facebook/Facebook reply hierarchy lives in the article aria-label, not DOM nesting]]

%% ai-graph-start %%

**Related notes:**
- [[Facebook reply hierarchy lives in the article aria-label, not DOM nesting]]
- [[Unanchored 'From' regex captures the profile name from Facebook's 'See more from' buttons]]
- [[Facebook reply-expander button label variants]]
- [[Facebook's Newest sort option mentions 'all comments' in its description — anchor the label regex]]
- [[Facebook reel comments are hidden behind the comment icon]]

%% ai-graph-end %%