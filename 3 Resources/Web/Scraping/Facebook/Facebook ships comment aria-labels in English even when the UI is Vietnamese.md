---
title: "Facebook ships comment aria-labels in English even when the UI is Vietnamese"
created: 2026-06-22
type: observation
status: seedling
source: "fb-info-project session 2026-06-22"
tags: [facebook, scraping, i18n, gotcha]
---

# Facebook ships comment aria-labels in English even when the UI is Vietnamese

When scraping Facebook comments, the comment/reply `aria-label`s come back in **English** ("Comment by …", "Reply by … to …'s comment") **even when the visible UI chrome is Vietnamese** (buttons like "Xem thêm phản hồi", "Trả lời"). The account/page language localizes the visible button text but the article aria-labels stayed English on a live Vietnamese-content post.

**Consequence for parsers:** match aria-labels with English patterns first (confirmed), and treat Vietnamese aria forms ("Phản hồi của X … của Y") as a best-effort fallback — do not assume aria-label locale follows the button-text locale. Always keep a structural fallback that at least marks "this is a reply" even when the parent name can't be parsed.

Surfaced while fixing the reply-to column in fb-info-project. Parent idea: [[Facebook reply hierarchy lives in the article aria-label, not DOM nesting]].

## Related

- [[3 Resources/Web/Scraping/Facebook/Facebook reply hierarchy lives in the article aria-label, not DOM nesting]]
