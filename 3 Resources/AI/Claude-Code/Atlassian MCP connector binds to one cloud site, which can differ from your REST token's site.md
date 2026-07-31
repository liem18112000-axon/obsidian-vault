---
ai_hash: d38e94af8e1800fd
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
aliases:
- Atlassian MCP grant is per-site - axonivy.atlassian.net not covered
created: 2026-07-23
entities: []
source: sessions 2026-07-23 (Kepler / LUZ tickets)
status: seedling
tags:
- claude-code
- atlassian
- jira
- mcp
- gotcha
- kepler
title: Atlassian MCP connector binds to one cloud site, which can differ from your
  REST token's site
type: lesson
---

# Atlassian MCP connector binds to one cloud site, which can differ from your REST token's site

The `claude.ai Atlassian` MCP connector is granted **per Atlassian cloud site, not per account**. `getAccessibleAtlassianResources` returns only the granted site's cloudId; any other site fails even though its URL resolves. In this environment only `leocdp.atlassian.net` is granted, so `getJiraIssue` on `axonivy.atlassian.net` (where LUZ-* tickets live) returns *"Cloud id … isn't explicitly granted by the user"*.

The nastier variant is a **silent split** when the app also talks to Jira via a REST API token (email + token) pointed at a different org: reads via REST hit org A, writes via the connector hit org B. A connector write-back against an org-A issue key fails with *"Issue does not exist or you do not have permission to see it"*, and a well-behaved agent then reports it created nothing (no error, just `commented:false, stories:[]`) - so it looks like the tool was "unavailable" rather than pointed at the wrong site.

**Diagnosis:** a cheap read-only headless probe - `claude -p --permission-mode dontAsk --allowedTools mcp__claude_ai_Atlassian__getJiraIssue … "fetch <KEY>, or state the exact error"`. An API error naming a site means a site mismatch, not a permissions/timeout problem (~5 turns vs a 6-min full write attempt).

**Fixes:** (a) re-authenticate the Atlassian connector in claude.ai settings and grant the missing site; or (b) do the write-back via Jira REST with the same token used for reads, so reads and writes always hit one site (you must then build createmeta-aware field payloads yourself).

**Workarounds that do NOT work:** unauthenticated WebFetch (SPA shell / login wall) and Playwright MCP (fresh browser profile, redirected to `id.atlassian.com` login) - unless you log in manually in the open Playwright window. See [[Jira issue HTML export view bypasses missing MCP grant]] for the export route that does work.

## Related

- [[Jira issue HTML export view bypasses missing MCP grant]]
- [[Long agentic API routes need the inner run timeout below the route maxDuration]]

%% ai-graph-start %%

**Related notes:**
- [[Jira issue HTML export view bypasses missing MCP grant]]
- [[claude.ai Atlassian MCP has Jira scopes only — Confluence returns 403 app-not-installed]]
- [[Vinnstack's Jira client avoids the Atlassian MCP connector]]
- [[Headless claude exit 0 does not mean the operation succeeded]]
- [[Verify kubectl context before GKE rollout - _context file can disagree]]

%% ai-graph-end %%