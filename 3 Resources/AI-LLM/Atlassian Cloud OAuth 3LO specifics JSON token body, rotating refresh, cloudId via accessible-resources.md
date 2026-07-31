---
title: "Atlassian Cloud OAuth 3LO specifics: JSON token body, rotating refresh, cloudId via accessible-resources"
created: 2026-07-01
type: lesson
status: seedling
source: "session 2026-07-01"
tags: [oauth, atlassian, jira, confluence, vinnstack]
---

# Atlassian Cloud OAuth 3LO specifics: JSON token body, rotating refresh, cloudId via accessible-resources

Atlassian (Jira/Confluence) Cloud OAuth 2.0 (3LO), and how it differs from Bitbucket OAuth: (1) Authorize at https://auth.atlassian.com/authorize with params audience=api.atlassian.com, client_id, scope (space-joined; MUST include offline_access to get a refresh token), redirect_uri, state, response_type=code, prompt=consent. (2) Token endpoint https://auth.atlassian.com/oauth/token takes a JSON body (client_id, client_secret, grant_type, code|refresh_token, redirect_uri) — NOT form-encoded like Bitbucket. (3) Refresh tokens ROTATE: each refresh returns a NEW refresh_token you must persist (unlike many providers where it stays fixed). (4) You do not call the site directly: after getting a token, GET https://api.atlassian.com/oauth/token/accessible-resources (Bearer) to get the cloudId (arr[0].id) + site url; then all REST goes to https://api.atlassian.com/ex/jira/{cloudId}/rest/api/2/... with Bearer (not the site host + Basic). (5) One-time setup: register an app at developer.atlassian.com with the callback URL. Scopes for read: read:jira-work read:jira-user read:confluence-content.summary offline_access. Reuses the same in-memory state->resolver login bridge as Bitbucket. Real case: Vinnstack atlassianProvider + atlassianJiraFetch (OAuth-first, email+API-token Basic fallback) in lib/authProviders.ts.
