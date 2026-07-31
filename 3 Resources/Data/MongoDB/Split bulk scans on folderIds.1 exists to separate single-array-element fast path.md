---
title: "Split bulk scans on folderIds.1 exists to separate single-array-element fast path"
created: 2026-06-10
type: howto
status: seedling
source: "luz-docs enhance-delete-folder-api, sprint 158 (2026-06-10)"
tags: [mongodb, query, arrays, projection, performance, pagination, luz-docs]
---

# Split bulk scans on folderIds.1 exists to separate single-array-element fast path

`{"arrayField.1": {$exists: true}}` matches documents whose array has **two or more elements** — the dotted index `.1` exists only then. That is a cheap, index-free way to partition a scan into a bulk fast path and an expensive per-item path, entirely inside MongoDB (no aggregation).

Two complementary filters for "documents referencing folder F":
- **Only in F** — exact array match `{folderIds: [F]}` (Mongo compares the whole array literally; with one element, order can't differ). Equivalently: `folderIds.1` does not exist.
- **In F plus at least one other** — `{folderIds: F, "folderIds.1": {$exists: true}}`. The two keys don't collide in the filter document, unlike trying to put two conditions under the same `folderIds` key.

Pattern from luz-docs folder deletion: single-membership documents need no per-document logic (their only folder is being deleted ⇒ deletable by definition), so they are collected by a paged `_id`-only-projection query straight into the deletable list. Only the multi-membership minority is fetched with a slim multi-field projection and run through per-document filtering.

**General shape:** when per-item processing exists only to handle a minority case, find a queryable predicate (`field.1 exists`, a flag, a type) that isolates that minority and stream the rest through a projection-trimmed bulk path.

## Related

- [[Validate with a count query for violators instead of loading all documents]]
- [[luz-docs folder delete filter double-fetched every subfolder]]
- [[Pass fetched objects down recursion instead of IDs to avoid N+1 re-fetch]]
