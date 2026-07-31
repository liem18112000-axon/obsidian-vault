---
ai_hash: 03e6a3f5b1dea2e8
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-30
entities: []
source: session 2026-07-30 leo-customer360
status: seedling
tags:
- atlassian
- confluence
- mcp
- storage-format
- howto
title: Embedding an image in Confluence requires uploading it as an attachment first
type: howto
---

# Embedding an image in Confluence requires uploading it as an attachment first

To embed a local image into a Confluence page you must **first upload it as a page attachment** — there is no way to push image bytes inline in either supported route.

**Storage format** (REST `body.storage`): reference the attachment by filename:
```xml
<ac:image ac:align="center" ac:width="900"><ri:attachment ri:filename="foo.png"/></ac:image>
```

**claude.ai Atlassian MCP `updateConfluencePage` (HTML+ format):** images are referenced by
`<div data-type="media" data-id="..." data-collection="...">`, and `data-id`/`data-collection` must be **copied from already-attached media** returned by `getConfluencePage` — you cannot invent them.

**Consequence:** in both routes the attachment must exist on the page before the reference resolves. Practical flow: upload the PNGs first, then (a) apply storage XML by filename, or (b) fetch the page to read the media data-ids and push HTML+.

Related access prerequisite: [[claude.ai Atlassian MCP has Jira scopes only — Confluence returns 403 app-not-installed]].

## Related

- [[claude.ai Atlassian MCP has Jira scopes only — Confluence returns 403 app-not-installed]]

%% ai-graph-start %%

**Related notes:**
- [[claude.ai Atlassian MCP has Jira scopes only — Confluence returns 403 app-not-installed]]
- [[Edit Confluence Cloud via authenticated Playwright browser when the Atlassian MCP app is not installed]]
- [[Read a private Confluence page via REST API with ATLASSIAN API token]]
- [[Rewrite Confluence page prose by element local-id to keep diagrams and structure intact]]
- [[Centering an embedded image in Obsidian]]

%% ai-graph-end %%