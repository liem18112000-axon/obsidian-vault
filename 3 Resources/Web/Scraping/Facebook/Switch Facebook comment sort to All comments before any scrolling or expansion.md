---
ai_hash: 674aeec00c113387
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-11
entities: []
source: fb-info-project session 2026-06-11
status: seedling
tags:
- facebook
- scraping
- performance
title: Switch Facebook comment sort to All comments before any scrolling or expansion
type: lesson
---

# Switch Facebook comment sort to All comments before any scrolling or expansion

Changing Facebook's comment sort mode re-renders the entire comment list from scratch. Any scrolling or "View more comments" expansion done **before** the switch is wasted work — the expanded "Most relevant" subset is thrown away the moment the mode changes to "All comments".

Correct order in a comment scraper: load page → dismiss popups → **switch sort mode (verified)** → then scroll/expand. In fb-info-project this was the difference between expanding the hidden-subset list for minutes and expanding the real, full list.

## Related

- [[Verify Facebook comment sort switch by re-reading the sort button label]]

%% ai-graph-start %%

**Related notes:**
- [[Verify Facebook comment sort switch by re-reading the sort button label]]
- [[Facebook's Newest sort option mentions 'all comments' in its description — anchor the label regex]]
- [[Distinguish absent control from missed click when expanding lazy lists]]
- [[Facebook post permalinks render the post twice — dialog plus a hidden page copy]]
- [[--max-expand caps comment batches not profile count; profile-visit phase dominates runtime]]

%% ai-graph-end %%