---
title: "Partition documents by folder membership with exact-array-match and array-index-exists filters"
created: 2026-06-10
type: howto
status: seedling
source: "session 2026-06-10"
tags: [mongodb, query, arrays, performance]
---

# Partition documents by folder membership with exact-array-match and array-index-exists filters

To split documents referencing a folder F into "only in F" vs "in F and others" entirely inside MongoDB, no aggregation needed:

- Only in F (array is exactly one element): exact array match `{folderIds: [F]}` — Mongo matches the whole array literally, and with a single element order cannot differ.
- In F plus at least one other: `{folderIds: F, "folderIds.1": {\: true}}` — the dotted index `.1` exists only when the array has 2+ elements, and the two keys don't collide in the filter document (vs. trying `folderIds: F` AND `folderIds: {\: ...}` which would be a duplicate key).

Useful when the size-1 class needs no further per-item logic (e.g. docs whose only folder is being deleted are deletable by definition) — fetch just their _ids with a projection and handle only the multi-membership minority in application code.

Applied in [[luz-docs folder delete filter double-fetched every subfolder]]; general principle: [[Pass fetched objects down recursion instead of IDs to avoid N+1 re-fetch]]

## Related

- [[luz-docs folder delete filter double-fetched every subfolder]]
- [[Pass fetched objects down recursion instead of IDs to avoid N+1 re-fetch]]
