---
title: "Jira issue HTML export view bypasses missing MCP grant"
created: 2026-07-23
type: howto
status: seedling
source: "session 2026-07-23"
tags: [jira, atlassian, mcp, claude-code, workaround]
---

# Jira issue HTML export view bypasses missing MCP grant

When the Atlassian MCP connector lacks a grant for a Jira site, a ticket can still be obtained via Jira's built-in HTML issue view, exported by anyone with browser access: `https://<site>.atlassian.net/si/jira.issueviews:issue-html/<KEY>/<KEY>.html` (print/save as PDF, then feed the PDF to Claude's Read tool — PDFs render including attachments/screenshots).

Used for LUZ-157476: MCP grant covered only leocdp.atlassian.net, WebFetch and Playwright hit the login wall, but the user exported the issue-html view as PDF into the repo docs folder and the full ticket (description, AC, sub-tasks, even an attached UI screenshot) was readable.

## Related
- [[Atlassian MCP grant is per-site - axonivy.atlassian.net not covered]]
