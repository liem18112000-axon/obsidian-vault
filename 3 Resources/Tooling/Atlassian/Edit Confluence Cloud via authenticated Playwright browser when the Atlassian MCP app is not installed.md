---
ai_hash: ffa3e7e66ed8137c
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-25
entities: []
source: session 2026-07-25
status: seedling
tags:
- confluence
- atlassian
- playwright
- mcp
- rest-api
- gotcha
title: Edit Confluence Cloud via authenticated Playwright browser when the Atlassian
  MCP app is not installed
type: howto
---

# Edit Confluence Cloud via authenticated Playwright browser when the Atlassian MCP app is not installed

When the Atlassian MCP app is not installed on a Confluence Cloud instance, its Confluence tools fail with 403 "The app is not installed on this instance" (and the OAuth token may only carry read/write:jira-work scopes, no Confluence scope). Workaround that works: drive the **authenticated Playwright MCP browser** to log into the site once, then call the Confluence REST API with same-origin `fetch()` from inside the page via `browser_evaluate` — the browser session cookies authenticate the calls, so no API token is needed.

- Read: `GET /wiki/rest/api/content/{id}?expand=body.storage,version` (returns storage-format HTML + current version number).
- Write: `PUT /wiki/rest/api/content/{id}` with body `{type:"page", title, version:{number: current+1}, body:{storage:{value, representation:"storage"}}}`, headers `Content-Type: application/json` and `X-Atlassian-Token: no-check`.

A `/wiki/x/<tinyid>` short URL yields the tiny id (e.g. `BYDD`); you can pass it as `pageId` to the getConfluencePage MCP tool, but it still 403s if the app is not installed — so the browser route is the fallback. `WebFetch` cannot read the page (it redirects to the Atlassian login and has no session).

Verified 2026-07-25 on https://leocdp.atlassian.net (space SCRUM, pageId 12812293).

See [[Rewrite Confluence page prose by element local-id to keep diagrams and structure intact]] for the safe-edit method, and [[Playwright MCP Browser is already in use lock kill only ms-playwright-mcp chrome to free it]] for the profile-lock gotcha.

## Related

- [[Rewrite Confluence page prose by element local-id to keep diagrams and structure intact]]
- [[Playwright MCP Browser is already in use lock kill only ms-playwright-mcp chrome to free it]]

%% ai-graph-start %%

**Related notes:**
- [[claude.ai Atlassian MCP has Jira scopes only — Confluence returns 403 app-not-installed]]
- [[Playwright MCP Browser is already in use lock kill only ms-playwright-mcp chrome to free it]]
- [[Read a private Confluence page via REST API with ATLASSIAN API token]]
- [[Rewrite Confluence page prose by element local-id to keep diagrams and structure intact]]
- [[Atlassian MCP connector binds to one cloud site, which can differ from your REST token's site]]

%% ai-graph-end %%