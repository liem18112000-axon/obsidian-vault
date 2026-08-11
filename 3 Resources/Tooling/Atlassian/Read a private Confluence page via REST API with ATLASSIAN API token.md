---
ai_hash: 6cb3e53483149b1e
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-27
entities: []
source: session 2026-07-27 (Ivy setup)
status: seedling
tags:
- atlassian
- confluence
- rest-api
- curl
- gotcha
title: Read a private Confluence page via REST API with ATLASSIAN API token
type: howto
---

# Read a private Confluence page via REST API with ATLASSIAN API token

When the Atlassian MCP/OAuth connection lacks access to a Confluence Cloud site (e.g. `axonivy.atlassian.net` returns "Cloud id ... isn't explicitly granted"), but the machine has `ATLASSIAN_EMAIL` + `ATLASSIAN_API_TOKEN` env vars for an account that *does* have access, read the page directly via HTTP Basic auth on the Confluence Cloud REST API.

**Steps**

1. Verify auth: `curl -s -u "$ATLASSIAN_EMAIL:$ATLASSIAN_API_TOKEN" https://SITE/wiki/rest/api/user/current` → expect HTTP 200.
2. Resolve a `/wiki/x/<id>` **tiny link** to a numeric pageId by following redirects: `curl -sIL -u ... https://SITE/wiki/x/<id>` and read the final URL (`.../pages/<pageId>/<Title>`).
3. Fetch content: `GET /wiki/rest/api/content/<pageId>?expand=body.view,version,space`. `body.view` is rendered HTML; `body.storage` is the editable XHTML source.
4. Attachments: `GET /wiki/rest/api/content/<pageId>/child/attachment` → take each `_links.download` (`/rest/api/content/<pageId>/child/attachment/att<ID>/download`) and `curl -L` it (the versioned `/wiki/download/attachments/...` query-string link often 401s; the REST attachment endpoint works).

**Gotcha:** `curl -u user:token ... -w "%{url_effective}"` prints the credentials embedded in the effective URL — it **leaks the token** into logs/transcripts. Never print `url_effective` with `-u`; rotate the token if it leaks.

This is the fallback when the OAuth-based Atlassian MCP tools cannot reach a site the user still has token access to.

## Related
[[Resources index]]

## Related

- [[Resources index]]

%% ai-graph-start %%

**Related notes:**
- [[Edit Confluence Cloud via authenticated Playwright browser when the Atlassian MCP app is not installed]]
- [[Atlassian Cloud OAuth 3LO specifics JSON token body, rotating refresh, cloudId via accessible-resources]]
- [[Atlassian MCP connector binds to one cloud site, which can differ from your REST token's site]]
- [[Jira issue HTML export view bypasses missing MCP grant]]
- [[claude.ai Atlassian MCP has Jira scopes only — Confluence returns 403 app-not-installed]]

%% ai-graph-end %%