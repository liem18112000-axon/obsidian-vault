---
title: "Bitbucket Cloud PR comment resolution is presence-of-object, not a boolean"
created: 2026-07-12
type: lesson
status: seedling
source: "vinnstack BDD Implement PR-comments feature, Bitbucket swagger.json, 2026-07-12"
tags: [bitbucket, rest-api, gotcha, vinnstack]
---

# Bitbucket Cloud PR comment resolution is presence-of-object, not a boolean

A Bitbucket Cloud `pullrequest_comment` marks "resolved" by the **presence of a nested `resolution` object** (`{type, user, created_on}`); an unresolved comment omits the key entirely. Check `!!comment.resolution` (or `"resolution" in comment`) — there is no `comment.resolved` field.

Adjacent fields worth filtering on:
- `pending` (plain boolean) — the comment belongs to an unsubmitted draft review (Bitbucket's "pending review comments"). Not real feedback yet; exclude it like `deleted: true`.
- `pullrequest.comment_count` — a denormalized counter on the PR resource, **not** derived from `/pullrequests/{id}/comments`. It can lag a real comment even after refresh, so compute any displayed count from the `/comments` list endpoint instead.

Verified against `https://api.bitbucket.org/swagger.json` (`definitions.pullrequest_comment` / `definitions.comment_resolution`) — the rendered developer.atlassian.com docs are a JS SPA a plain fetch can't read, but the swagger.json is static and directly curlable. Good default for exact field shapes on any Atlassian API.

## Related

- [[Bitbucket Cloud API pagination returns full URLs in 'next', not relative paths]]
- [[Regenerate-from-review-feedback pattern: reuse the branch/PR, don't open a new one]]
