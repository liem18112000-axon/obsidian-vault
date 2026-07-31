---
ai_hash: 9dc537310c965c6b
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-11
entities: []
source: fb-info-project session 2026-06-11
status: evergreen
tags:
- testing
- fakes
- regression
- scraping
title: Build test fakes from verbatim production data, decoys included
type: lesson
---

# Build test fakes from verbatim production data, decoys included

A fake-DOM unit test passed while the same code failed on the real page, because the fake contained only the *target* element — not its neighbors. The real Facebook sort menu has a decoy: the "Newest" item's description contains the words "all comments", which an unanchored regex matched first. A fake holding only the "All comments" item can never catch that.

Lesson: when faking external UI/data for tests, copy **verbatim production strings for the whole neighborhood** (siblings, descriptions, decoys), not just the element under test. Selector and regex bugs are almost always *disambiguation* bugs — a fake without competing candidates tests nothing about disambiguation. After any live debugging session, feed the real strings you captured back into the fakes as regression data.

## Related

- [[Facebook's Newest sort option mentions 'all comments' in its description — anchor the label regex]]

%% ai-graph-start %%

**Related notes:**
- [[Facebook's Newest sort option mentions 'all comments' in its description — anchor the label regex]]
- [[LLM-picked UI actions can be verified mechanically but not semantically]]
- [[Verify Facebook comment sort switch by re-reading the sort button label]]
- [[Self-healing scraper selectors — LLM fallback only on verified failure, then cache]]
- [[Facebook post permalinks render the post twice — dialog plus a hidden page copy]]

%% ai-graph-end %%