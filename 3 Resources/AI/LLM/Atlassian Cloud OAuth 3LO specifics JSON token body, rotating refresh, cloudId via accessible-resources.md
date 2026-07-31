---
ai_hash: 4a981eafab595fa1
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-01
entities: []
source: session 2026-07-01
status: seedling
tags:
- oauth
- atlassian
- jira
- confluence
- vinnstack
title: 'Atlassian Cloud OAuth 3LO specifics: JSON token body, rotating refresh, cloudId
  via accessible-resources'
type: lesson
---

# Atlassian Cloud OAuth 3LO specifics: JSON token body, rotating refresh, cloudId via accessible-resources

Four ways Atlassian (Jira/Confluence) Cloud OAuth 2.0 (3LO) differs from a generic/Bitbucket flow, each of which breaks a naive implementation:

1. **Authorize** at `https://auth.atlassian.com/authorize` needs `audience=api.atlassian.com` plus `client_id`, `scope` (space-joined; MUST include `offline_access` or you get no refresh token), `redirect_uri`, `state`, `response_type=code`, `prompt=consent`.
2. **Token endpoint** `https://auth.atlassian.com/oauth/token` takes a **JSON body** (`client_id`, `client_secret`, `grant_type`, `code`|`refresh_token`, `redirect_uri`) — NOT form-encoded.
3. **Refresh tokens ROTATE**: every refresh returns a NEW `refresh_token` that you must persist over the old one.
4. **You never call the site host.** After the token, `GET https://api.atlassian.com/oauth/token/accessible-resources` (Bearer) yields the `cloudId` (`arr[0].id`) + site url; all REST then goes to `https://api.atlassian.com/ex/jira/{cloudId}/rest/api/2/...` with Bearer, not site-host + Basic.

One-time setup: register an app at developer.atlassian.com with the callback URL. Read scopes: `read:jira-work read:jira-user read:confluence-content.summary offline_access`. Login itself reuses the same in-memory state->resolver bridge as Bitbucket. Real case: Vinnstack `atlassianProvider` + `atlassianJiraFetch` (OAuth-first, email+API-token Basic fallback) in `lib/authProviders.ts`.

## Related

- [[Local-app OAuth bridges the browser callback to the waiting login via an in-memory state-to-resolver map]]

%% ai-graph-start %%

**Related notes:**
- [[Local-app OAuth bridges the browser callback to the waiting login via an in-memory state-to-resolver map]]
- [[Verifying an API credential 401 means invalid, 403 means valid but scope-limited]]
- [[Read a private Confluence page via REST API with ATLASSIAN API token]]
- [[Vinnstack auth providers two patterns and the rule for adding one]]
- [[Edit Confluence Cloud via authenticated Playwright browser when the Atlassian MCP app is not installed]]

%% ai-graph-end %%