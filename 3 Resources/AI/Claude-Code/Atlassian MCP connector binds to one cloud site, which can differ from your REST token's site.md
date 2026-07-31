---
tags: [claude-code, atlassian, jira, mcp, gotcha, kepler]
---

# Atlassian MCP connector binds to one cloud site, which can differ from your REST token's site

The `claude.ai Atlassian` MCP connector authenticates to **one Atlassian cloud instance** (the account you linked). `getAccessibleAtlassianResources` returns only that site's cloudId. If your app ALSO talks to Jira via a REST API token (email + token) pointed at a *different* org, then:

- **Reads via REST** hit org A (e.g. `axonivy.atlassian.net`).
- **Writes via the MCP connector** hit org B (e.g. `leocdp.atlassian.net`).

They silently target different Jira sites. A write-back (create issue / comment) run through the connector against org B will fail on an org-A issue key with *"Issue does not exist or you do not have permission to see it"* — and a well-behaved agent then reports it created nothing (no error, just `commented:false, stories:[]`), so it looks like the tool was "unavailable" when it was actually pointed at the wrong site.

**Diagnosis trick:** run a cheap, read-only headless probe — `claude -p --permission-mode dontAsk --allowedTools mcp__claude_ai_Atlassian__getJiraIssue ... "fetch <KEY>, or state the exact error"`. If it returns an API error naming a site (`leocdp.atlassian.net`) rather than a permission block, you've found a site mismatch, not a permissions/timeout problem. This ran in ~5 turns vs a 6-min full write attempt.

**Fixes:** (a) re-authorize the claude.ai Atlassian connector for the correct org, or (b) do the write-back via Jira REST with the same token used for reads, so reads and writes always hit the same site (provider-independent, but you must build the createmeta-aware field payloads yourself).

Related: [[Long agentic API routes need the inner run timeout below the route maxDuration]].
