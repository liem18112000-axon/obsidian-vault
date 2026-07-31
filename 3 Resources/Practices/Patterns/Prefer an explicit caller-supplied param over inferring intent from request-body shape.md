---
title: "Prefer an explicit caller-supplied param over inferring intent from request-body shape"
created: 2026-07-09
type: lesson
status: seedling
source: "luz_docs estimatedcount feature, 2026-07-09 — deleted FolderCountQueryMatcher in favour of the existing folder-id query param"
tags: [api-design, rest, simplicity]
---

# Prefer an explicit caller-supplied param over inferring intent from request-body shape

When a feature applies only to a narrow case of a general-purpose endpoint (e.g. "the fast estimate works only when the count is scoped to one folder", on a `/count` endpoint taking an arbitrary query DSL), do **not** detect that case by pattern-matching the payload the caller already sent. Take the fact as an explicit parameter.

Cost of the shape-inference route: a dedicated matcher class plus rejection-path tests for every way the shape can fail to match; brittleness to any evolution of the query DSL; and an undocumented contract — the caller must still structure the request just so, with nothing in the API saying it.

Concretely: luz_docs' HyperLogLog count-estimate first detected "folder-scoped-only count" by parsing the `/documents/count` body's SearchQuery DSL for `{"or":[{"term":{"folderIds":X}}]}`. Replacing it with a direct read of the existing (previously unused by count) folder-id query param deleted the whole matcher class and its test file with no loss of correctness.

Rule of thumb: before writing a shape detector over a generic payload, check whether the caller can just tell you — especially if a same-purpose parameter already exists on that resource.

## Related

- [[Pick the variant matching the data you already hold, not the triggering operation]]
