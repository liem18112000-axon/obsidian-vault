---
title: "claude.ai Atlassian MCP has Jira scopes only — Confluence returns 403 app-not-installed"
created: 2026-07-30
type: gotcha
status: seedling
source: "session 2026-07-30 leo-customer360"
tags: [atlassian, confluence, mcp, claude-code, gotcha]
---

# claude.ai Atlassian MCP has Jira scopes only — Confluence returns 403 app-not-installed

The claude.ai Atlassian MCP connection to `leocdp.atlassian.net` is authorized with **Jira scopes only** (`read:jira-work`, `write:jira-work`). Every Confluence tool call — `getConfluencePage`, `getConfluenceSpaces`, `updateConfluencePage` — fails with HTTP **403 "The app is not installed on this instance"**, regardless of whether you pass the site hostname or the UUID cloudId.

**Why:** the OAuth grant / installed app covers Jira but not Confluence on this site.

**Fix:** re-authorize the Atlassian MCP via `/mcp` granting Confluence read/write, or have a Confluence site admin install the Atlassian app on the instance.

**Also blocked:** WebFetch on a `/wiki/x/<tiny>` link just 302-redirects to the Atlassian login (private page), so WebFetch cannot read Confluence content either. There is no read path to Confluence until access is fixed.

See [[Embedding an image in Confluence requires uploading it as an attachment first]] for the related image-embedding constraint once access works.

## Related

- [[Embedding an image in Confluence requires uploading it as an attachment first]]
