---
title: "Non-simple Mongo collation forces in-memory sort that hits the 32MB limit on deep paging"
created: 2026-06-26
type: lesson
status: seedling
source: "session 2026-06-26, commit 0361009"
tags: [mongodb, collation, performance, luz-docs, gotcha]
---

# Non-simple Mongo collation forces in-memory sort that hits the 32MB limit on deep paging

MongoDB applies an index only when the query's collation matches the index's collation. A query that requests a **non-simple collation** (e.g. case-insensitive) on a field indexed with the default *simple* collation cannot use that index for sorting, so Mongo falls back to an **in-memory sort**. In-memory sorts are capped at **32MB**; once a deep offset/skip scan accumulates enough documents the sort exceeds the cap and the server errors.

Real instance (luz-docs commit 0361009): the delete-folder discovery scans sorted by `_id` but carried a non-simple collation that did not match the `_id` index. Combined with deep offset paging, this blew the 32MB sort limit and made luz-jsonstore return **500 once skip passed ~5500 docs**. The fix: drop the collation on those scans (pass null) so the `_id` sort stays index-backed and streams without an in-memory sort.

Takeaway: for large index-backed paged scans, do **not** attach a collation that differs from the index's — the simple collation keeps the sort on the index. Reserve non-simple collations for queries that genuinely need them and have a matching collated index.

Related: [[Bulk write paths in folder delete only engage with more than one document]]

## Related

- [[Bulk write paths in folder delete only engage with more than one document]]
