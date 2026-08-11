---
ai_hash: 5cbbd3d6d0483764
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-12
entities: []
source: vinnstack BDD Implement PR-comments feature, 2026-07-12
status: seedling
tags:
- bitbucket
- rest-api
- pagination
- gotcha
- vinnstack
title: Bitbucket Cloud API pagination returns full URLs in 'next', not relative paths
type: lesson
---

# Bitbucket Cloud API pagination returns full URLs in 'next', not relative paths

Bitbucket Cloud's REST API (api.bitbucket.org/2.0) paginates list endpoints (e.g. PR comments, PRs) with a `next` field in the response body that is a COMPLETE absolute URL (e.g. `https://api.bitbucket.org/2.0/repositories/ws/repo/pullrequests/12/comments?page=2`), not a relative path or a bare `page` query param.

This matters if you have a thin request wrapper that always prefixes a fixed base URL onto whatever path you pass it (a common pattern — e.g. `bitbucketReq(path)` that does `fetch(\`https://api.bitbucket.org/2.0${path}\`)`). Passing the raw `next` value straight into such a wrapper would double up the base URL and 404. You have to strip the known base prefix off `next` before feeding it back into the same wrapper, e.g. `next.replace("https://api.bitbucket.org/2.0", "")`.

Applied in vinnstack's `lib/bdd/prComments.ts` (`fetchPrComments`), which follows `next` links in a loop (capped at 20 pages as a sane bound) to collect every comment on a PR before feeding them to an LLM as review feedback.

General lesson: before looping over a paginated REST API's "next" field, check whether it's a full URL or a relative path/cursor — assuming one when the API actually gives the other silently breaks pagination past page 1.

%% ai-graph-start %%

**Related notes:**
- [[Bitbucket Cloud PR comment resolution is presence-of-object, not a boolean]]
- [[Bitbucket Cloud pull-request REST API shape]]
- [[Regenerate-from-review-feedback pattern reuse the branchPR, don't open a new one]]
- [[gh CLI is GitHub-only, not Bitbucket-aware]]
- [[Local-app OAuth bridges the browser callback to the waiting login via an in-memory state-to-resolver map]]

%% ai-graph-end %%