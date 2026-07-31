---
title: "Switch Facebook comment sort to All comments before any scrolling or expansion"
created: 2026-06-11
type: lesson
status: seedling
source: "fb-info-project session 2026-06-11"
tags: [facebook, scraping, performance]
---

# Switch Facebook comment sort to All comments before any scrolling or expansion

Changing Facebook's comment sort mode re-renders the entire comment list from scratch. Any scrolling or "View more comments" expansion done **before** the switch is wasted work — the expanded "Most relevant" subset is thrown away the moment the mode changes to "All comments".

Correct order in a comment scraper: load page → dismiss popups → **switch sort mode (verified)** → then scroll/expand. In fb-info-project this was the difference between expanding the hidden-subset list for minutes and expanding the real, full list.

## Related

- [[Verify Facebook comment sort switch by re-reading the sort button label]]
