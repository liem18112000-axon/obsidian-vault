---
ai_hash: ff056d78daae20b0
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-12
entities: []
source: Vinnstack BDD implement-stage debugging, 2026-07-12
status: seedling
tags:
- bitbucket
- rest-api
- pull-requests
title: Bitbucket Cloud pull-request REST API shape
type: howto
---

# Bitbucket Cloud pull-request REST API shape

Bitbucket Cloud's REST API (v2.0) exposes pull requests under `/2.0/repositories/{workspace}/{repo_slug}/pullrequests`, authenticated with Basic auth (username + an app password, base64-encoded into the Authorization header) — the same credential shape used for other Bitbucket REST calls.

**Look up an existing PR for a branch** (to avoid creating a duplicate): `GET .../pullrequests?q=source.branch.name="<branch>" AND state="OPEN"` (the `q` value is a Bitbucket query-language expression, URL-encoded). The response's `values[]` array holds matches; each has `links.html.href` as the human-facing PR URL.

**Create a PR**: `POST .../pullrequests` with a JSON body:
```json
{
  "title": "...",
  "description": "...",
  "source": {"branch": {"name": "feature-branch"}},
  "destination": {"branch": {"name": "main"}},
  "close_source_branch": false
}
```
The response's `links.html.href` is again the PR's URL. On failure, the error body is shaped `{"type":"error","error":{"message":"..."}}` — surface `error.message` directly, it's usually specific and actionable (e.g. "destination branch not found").

Workspace + repo_slug can be parsed straight out of the repo's own `origin` remote URL rather than hardcoded — both the HTTPS form (`https://user@bitbucket.org/workspace/repo.git`) and SSH form (`git@bitbucket.org:workspace/repo.git`) match `bitbucket\.org[:/]([^/]+)/([^/]+?)(?:\.git)?/?$`.

Used this to replace a `gh`-CLI-based PR-opener in Vinnstack's lib/bdd/implementGit.ts once it turned out the target repo is Bitbucket-hosted, not GitHub — see [[gh CLI is GitHub-only, not Bitbucket-aware]]. Bonus: since this is a plain JSON POST body (not CLI argv), the PR description no longer needs a temp `--body-file` workaround for argv-length limits — it just goes straight in the request body.

%% ai-graph-start %%

**Related notes:**
- [[gh CLI is GitHub-only, not Bitbucket-aware]]
- [[Bitbucket Cloud PR comment resolution is presence-of-object, not a boolean]]
- [[Bitbucket Cloud API pagination returns full URLs in 'next', not relative paths]]
- [[Clone a Bitbucket repo with an app password without leaking it (inline credential helper)]]
- [[Regenerate-from-review-feedback pattern reuse the branchPR, don't open a new one]]

%% ai-graph-end %%