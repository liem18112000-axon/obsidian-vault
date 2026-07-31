---
title: "Atlassian MCP grant is per-site - axonivy.atlassian.net not covered"
created: 2026-07-23
type: lesson
status: seedling
source: "session 2026-07-23"
tags: [claude-code, mcp, atlassian, jira, gotcha]
---

# Atlassian MCP grant is per-site - axonivy.atlassian.net not covered

The claude.ai Atlassian MCP connector grants access **per Atlassian site**, not per account. In this environment only leocdp.atlassian.net is granted; axonivy.atlassian.net (where LUZ-* tickets live) returns "Cloud id ... isn't explicitly granted by the user" from getJiraIssue even though the URL resolves.

Workarounds that do NOT work: unauthenticated WebFetch (SPA shell/login wall) and Playwright MCP (fresh browser profile, redirected to id.atlassian.com login). Fix: re-authenticate the Atlassian connector in claude.ai settings and select/grant the axonivy site, or log in manually in the Playwright browser window when it is open.

## Related
- [[DECLINED status falls through invoice charge-failure handling in luz_store]]
