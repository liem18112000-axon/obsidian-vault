---
ai_hash: 30ae78fac09e3b0a
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-11
entities: []
source: fb-info-project live debug 2026-06-11
status: evergreen
tags:
- facebook
- playwright
- scraping
- gotcha
title: Facebook post permalinks render the post twice — dialog plus a hidden page
  copy
type: lesson
---

# Facebook post permalinks render the post twice — dialog plus a hidden page copy

Opening a Facebook post permalink renders the post **twice**: the visible copy inside a `role=dialog` ("<Page>'s post") and a hidden copy on the page behind it. Both copies carry the same interactive controls (sort chip, buttons), so role/text locators return **two** matches and `.first` may pick either one.

Consequences for scrapers:
- Don't assume one match: iterate *all* matching controls and act on each; hidden clones fail to click (Playwright can't scroll them into view) and can be skipped.
- Treat "one copy switched, the other is unclickable" as success — the unclickable one is the dead background render.
- A verification check like 'some control now shows state X' must tolerate the stale clone still showing the old state.

## Related

- [[Verify Facebook comment sort switch by re-reading the sort button label]]
- [[Facebook's Newest sort option mentions 'all comments' in its description — anchor the label regex]]

%% ai-graph-start %%

**Related notes:**
- [[Verify Facebook comment sort switch by re-reading the sort button label]]
- [[Facebook's Newest sort option mentions 'all comments' in its description — anchor the label regex]]
- [[Facebook reel comments are hidden behind the comment icon]]
- [[Switch Facebook comment sort to All comments before any scrolling or expansion]]
- [[Facebook sharev links can resolve to reels — classify after the redirect]]

%% ai-graph-end %%