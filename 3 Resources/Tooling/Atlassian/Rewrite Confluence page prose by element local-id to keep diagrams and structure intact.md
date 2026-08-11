---
ai_hash: 0e9b9e75e5fdb3a5
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-25
entities: []
source: session 2026-07-25
status: seedling
tags:
- confluence
- storage-format
- safe-edit
- technique
title: Rewrite Confluence page prose by element local-id to keep diagrams and structure
  intact
type: lesson
---

# Rewrite Confluence page prose by element local-id to keep diagrams and structure intact

To reword only the prose of a large Confluence page while keeping every diagram, image, table, layout and macro **byte-identical**, do targeted text replacement keyed by each block elements `local-id` — do NOT re-author the whole storage body (too error-prone; risks dropping an `<ac:image>` or breaking XML).

Method that worked:
1. Fetch the storage-format body once and extract a worklist of every `<p>`/heading with its `local-id` and plain text.
2. Build a map `{ local-id: newInnerHTML }` of just the blocks to change.
3. Replace each with regex `(<p [^>]*local-id="ID"[^>]*>)([^]*?)(</p>)` → keep groups 1 and 3, swap group 2. `[^]` matches any char incl. newlines; the `<p ` (with space) avoids matching `<pre>`.
4. Validate LOCALLY with Node first: assert each id occurs exactly once and every regex matches (zero misses), and diff pre/post counts of structural markers (`<ac:image`, `ri:attachment`, `<table`, `<tr`, `<td`, `ac:layout-section`, `structured-macro`) — they must be identical.
5. Gate the live PUT on exact expected pre/post body **lengths** so it only commits if the in-browser transform reproduced the validated local result.

Gotchas: Confluence storage keeps HTML entities literally (`&mdash;` `&rarr;` `&amp;` `&nbsp;`), so match on those; in replacement text avoid raw `&`/`<`/`>` (write "and"/"under"/"over") to stay valid storage XML. Self-closing empty paragraphs `<p local-id="x" />` have no `</p>` — never target them; targeting normal paragraphs is safe because `<p>` never nests. Leaving section headings untouched (only rewording body prose) keeps stable identifiers like "Proposal B" intact.

Parent technique: [[Edit Confluence Cloud via authenticated Playwright browser when the Atlassian MCP app is not installed]].

## Related

- [[Edit Confluence Cloud via authenticated Playwright browser when the Atlassian MCP app is not installed]]

%% ai-graph-start %%

**Related notes:**
- [[Edit Confluence Cloud via authenticated Playwright browser when the Atlassian MCP app is not installed]]
- [[TextQuoteSelector anchoring survives document regeneration]]
- [[Embedding an image in Confluence requires uploading it as an attachment first]]
- [[Inject anchored inline comments into LLM regeneration prompts as quoted passages]]

%% ai-graph-end %%