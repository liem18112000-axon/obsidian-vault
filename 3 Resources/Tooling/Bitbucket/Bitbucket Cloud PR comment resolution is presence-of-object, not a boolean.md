---
ai_hash: e533471a42833589
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-12
entities: []
source: vinnstack BDD Implement PR-comments feature, Bitbucket swagger.json, 2026-07-12
status: seedling
tags:
- bitbucket
- rest-api
- gotcha
- vinnstack
title: Bitbucket Cloud PR comment resolution is presence-of-object, not a boolean
type: lesson
---

# Bitbucket Cloud PR comment resolution is presence-of-object, not a boolean

A Bitbucket Cloud `pullrequest_comment` marks "resolved" by the **presence of a nested `resolution` object** (`{type, user, created_on}`); an unresolved comment omits the key entirely. Check `!!comment.resolution` (or `"resolution" in comment`) — there is no `comment.resolved` field.

Adjacent fields worth filtering on:
- `pending` (plain boolean) — the comment belongs to an unsubmitted draft review (Bitbucket's "pending review comments"). Not real feedback yet; exclude it like `deleted: true`.
- `pullrequest.comment_count` — a denormalized counter on the PR resource, **not** derived from `/pullrequests/{id}/comments`. It can lag a real comment even after refresh, so compute any displayed count from the `/comments` list endpoint instead.

Verified against `https://api.bitbucket.org/swagger.json` (`definitions.pullrequest_comment` / `definitions.comment_resolution`) — the rendered developer.atlassian.com docs are a JS SPA a plain fetch can't read, but the swagger.json is static and directly curlable. Good default for exact field shapes on any Atlassian API.

## Related

- [[Bitbucket Cloud API pagination returns full URLs in 'next', not relative paths]]
- [[3 Resources/AI/Agents/patterns/Regenerate-from-review-feedback pattern reuse the branchPR, don't open a new one]]

%% ai-graph-start %%

**Related notes:**
- [[Bitbucket Cloud API pagination returns full URLs in 'next', not relative paths]]
- [[Bitbucket Cloud pull-request REST API shape]]
- [[Regenerate-from-review-feedback pattern reuse the branchPR, don't open a new one]]
- [[Re-triggering GitHub Copilot PR review via API and its quota-limit gotcha]]
- [[Bitbucket PR merge lags git fetch; don't conclude not-merged from one originmain check]]

%% ai-graph-end %%