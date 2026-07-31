---
title: "Prefer an explicit caller-supplied param over inferring intent from request-body shape"
created: 2026-07-09
type: lesson
status: seedling
source: "luz_docs estimatedcount feature, 2026-07-09 — deleted FolderCountQueryMatcher in favor of the existing folder-id query param"
tags: [api-design, rest, simplicity]
---

# Prefer an explicit caller-supplied param over inferring intent from request-body shape

When a feature only applies to a narrow case of a general-purpose endpoint (e.g. "fast estimate only works when scoped to one folder" on a generic /count endpoint that accepts an arbitrary query DSL), there are two ways to detect whether the narrow case applies:

1. Infer it from the shape of whatever the caller already sent (e.g. parse the request body's nested query object, pattern-match for "exactly one OR clause containing a single folderIds term and nothing else").
2. Add a new, explicit parameter for exactly that piece of information and require/accept it directly from the caller.

Option 1 feels appealing because it avoids a new API parameter, but it: (a) requires writing and testing a whole shape-matching component (a dedicated matcher class, several rejection-path unit tests for every way the shape can fail to match), (b) is brittle to any evolution of the general query DSL, and (c) still needs the caller to structure their request in a very specific way that they have to know about even though nothing in the API contract documents it.

Option 2 is simpler in every dimension once there's already a natural param to reuse (or one is cheap to add): the caller states the fact directly, the server does not need to reverse-engineer intent from an unrelated general-purpose field, and the whole matcher class disappears.

Concretely: in luz_docs' HyperLogLog count-estimate feature, the first design detected "is this a folder-scoped-only count" by parsing the /documents/count request body's SearchQuery DSL for a specific {"or":[{"term":{"folderIds":X}}]} shape. This was replaced with just reading the existing (previously-unused-by-count) folder-id query param directly -- deleting an entire matcher class and its test file, with no loss of correctness, because the endpoint already had a parameter that said exactly what was needed.

Rule of thumb: before writing a shape-detector/inferencer over a generic payload, check whether the caller can just tell you the fact directly via a parameter -- especially if a same-purpose parameter already exists elsewhere on the same resource.

## Related

- [[Shared aggregate write targets need CAS]]
- [[not plain $set]]
