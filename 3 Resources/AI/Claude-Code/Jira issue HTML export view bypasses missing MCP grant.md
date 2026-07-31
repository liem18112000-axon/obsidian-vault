---
ai_hash: e7856fc815733f40
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-23
entities: []
source: session 2026-07-23
status: seedling
tags:
- jira
- atlassian
- mcp
- claude-code
- workaround
title: Jira issue HTML export view bypasses missing MCP grant
type: howto
---

# Jira issue HTML export view bypasses missing MCP grant

When the Atlassian MCP connector lacks a grant for a Jira site, a ticket can still be obtained via Jira's built-in HTML issue view, exported by anyone with browser access: `https://<site>.atlassian.net/si/jira.issueviews:issue-html/<KEY>/<KEY>.html` (print/save as PDF, then feed the PDF to Claude's Read tool — PDFs render including attachments/screenshots).

Used for LUZ-157476: MCP grant covered only leocdp.atlassian.net, WebFetch and Playwright hit the login wall, but the user exported the issue-html view as PDF into the repo docs folder and the full ticket (description, AC, sub-tasks, even an attached UI screenshot) was readable.

## Related
- [[Atlassian MCP connector binds to one cloud site, which can differ from your REST token's site]]

%% ai-graph-start %%

**Related notes:**
- [[Atlassian MCP connector binds to one cloud site, which can differ from your REST token's site]]
- [[claude.ai Atlassian MCP has Jira scopes only — Confluence returns 403 app-not-installed]]
- [[Edit Confluence Cloud via authenticated Playwright browser when the Atlassian MCP app is not installed]]
- [[Read a private Confluence page via REST API with ATLASSIAN API token]]
- [[Vinnstack's Jira client avoids the Atlassian MCP connector]]

%% ai-graph-end %%