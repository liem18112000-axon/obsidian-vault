---
ai_hash: 3e37aa4187a452a8
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-21
entities: []
source: session 2026-07-21
status: seedling
tags:
- obsidian
- gotcha
- skill
title: create_note.py splits --link on commas, breaking comma-titled wikilinks
type: lesson
---

# create_note.py splits --link on commas, breaking comma-titled wikilinks

The obsidian-note skill's create_note.py splits each --link value on commas, so a note title containing a comma (e.g. "plan: 5 scenarios, all automated") gets written as two broken wikilinks in the Related section. Workaround: avoid commas in note titles you intend to link, or repair the Related list manually after creation.

%% ai-graph-start %%

**Related notes:**
- [[Comma-split wikilinks leave dead fragment links in Related blocks]]
- [[Resolving a wikilink by basename truncates titles containing a slash]]

%% ai-graph-end %%