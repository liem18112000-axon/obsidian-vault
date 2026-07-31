---
title: "Bitbucket Cloud PR comment resolution is presence-of-object, not a boolean"
created: 2026-07-12
type: lesson
status: seedling
source: "vinnstack BDD Implement PR-comments feature, Bitbucket swagger.json, 2026-07-12"
tags: [bitbucket, rest-api, gotcha, vinnstack]
---

# Bitbucket Cloud PR comment resolution is presence-of-object, not a boolean

In Bitbucket Cloud's REST API, a `pullrequest_comment` object represents "resolved" as the PRESENCE of a nested `resolution` object (`{type, user, created_on}`), not a boolean flag. An unresolved comment simply omits the `resolution` key entirely. So the correct check in code is `!!comment.resolution` (or `"resolution" in comment`), not something like `comment.resolved === true` (that field doesn't exist).

A separate, easily-confused field: `pending` (a plain boolean) marks a comment that belongs to an unsubmitted/draft code review — Bitbucket's equivalent of GitHub's "pending review comments." A `pending: true` comment isn't real feedback yet (the reviewer hasn't submitted their review), so it should usually be excluded from anything that reacts to "reviewer feedback," same as a `deleted: true` comment.

Verified directly against Bitbucket's own OpenAPI/swagger definition (`https://api.bitbucket.org/swagger.json`, `definitions.pullrequest_comment` / `definitions.comment_resolution`) rather than inferred from docs prose — the rendered developer.atlassian.com docs page is a JS-rendered SPA that doesn't expose the raw schema to a simple fetch, but the swagger.json is a static, complete, directly-curlable source of truth for exact field shapes.

Separately: the `pullrequest` object's own `comment_count` field is a DIFFERENT thing — a denormalized summary counter on the PR resource itself, not derived from the live `/pullrequests/{id}/comments` list. In vinnstack's BDD Implement tab, this counter was being shown as the operator-facing "N comments" figure, but a real comment sometimes wasn't reflected in it even after a manual refresh. The fix was to stop trusting that summary field and instead compute the displayed count from the same authoritative `/comments` list endpoint already used to feed comments into the "regenerate from PR feedback" flow — same data, so what's displayed always matches what would actually be consumed.

Related: [[Bitbucket Cloud API pagination returns full URLs in 'next', not relative paths]], [[Regenerate-from-review-feedback pattern: reuse the branch/PR, don't open a new one]].

## Related

- [[Bitbucket Cloud API pagination returns full URLs in 'next']]
- [[not relative paths]]
- [[Regenerate-from-review-feedback pattern: reuse the branch/PR]]
- [[don't open a new one]]
