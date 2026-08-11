---
ai_hash: a85b134233d83c99
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-31
entities: []
source: vault PARA reorganization 2026-07-31
status: seedling
tags:
- obsidian
- wikilinks
- automation
- gotcha
title: Comma-split wikilinks leave dead fragment links in Related blocks
type: gotcha
---

# Comma-split wikilinks leave dead fragment links in Related blocks

An automated note-writing pass that builds a `## Related` block by splitting a string on commas will shatter any note title that contains one, emitting a dead link per fragment on consecutive bullets:

```markdown
- [[ngram trigram prefilter reads the built mongo query]]
- [[not the raw payload]]
```

The real note is `ngram trigram prefilter reads the built mongo query, not the raw payload`. Both bullets are dead, and because they *look* like ordinary list items the damage hides in plain sight — this vault carried ~100 of them across 147 files.

**Safe repair.** Walk consecutive `- [[...]]` bullet lines; when the first does not resolve, try rejoining it with the next one (and then the next two) using `, ` / `: ` / `; `, and **only commit the merge if the joined title actually resolves to a real note**. That resolution check is what makes it safe — a legitimate list of distinct links never accidentally concatenates into an existing title.

Two guards worth having:

- skip any fragment containing `/` — a genuine split fragment never has a path separator. Without this I collapsed three valid links into one, because the resolver matched the *basename* of the joined path-like string.
- match the whole joined string as a title; never fall back to its basename.

Related failure mode: [[Resolving a wikilink by basename truncates titles containing a slash]].

## Related

- [[Resolving a wikilink by basename truncates titles containing a slash]]
- [[Measure a broken-link baseline before a mass vault refactor]]

%% ai-graph-start %%

**Related notes:**
- [[Resolving a wikilink by basename truncates titles containing a slash]]
- [[create_note.py splits --link on commas, breaking comma-titled wikilinks]]
- [[Measure a broken-link baseline before a mass vault refactor]]

%% ai-graph-end %%