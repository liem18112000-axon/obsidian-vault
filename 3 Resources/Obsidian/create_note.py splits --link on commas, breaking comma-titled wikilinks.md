---
title: "create_note.py splits --link on commas, breaking comma-titled wikilinks"
created: 2026-07-21
type: lesson
status: seedling
source: "session 2026-07-21"
tags: [obsidian, gotcha, skill]
---

# create_note.py splits --link on commas, breaking comma-titled wikilinks

The obsidian-note skill's create_note.py splits each --link value on commas, so a note title containing a comma (e.g. "plan: 5 scenarios, all automated") gets written as two broken wikilinks in the Related section. Workaround: avoid commas in note titles you intend to link, or repair the Related list manually after creation.
