---
title: "Removing the union of array values per document is safe because absent values are no-ops"
created: 2026-06-10
type: argument
status: seedling
source: "luz-docs enhance-delete-folder-api, sprint 158 (2026-06-10)"
tags: [mongodb, batching, invariant, luz-docs]
---

# Removing the union of array values per document is safe because absent values are no-ops

To collapse N per-document "remove these specific values from this document's array" updates into one `updateMany`, you can apply the **union** of all values-to-remove to every targeted document. This is correct because removal is idempotent on absence: stripping a value a document never contained is a no-op, so each document ends up exactly as if only its own subset had been removed.

The invariant to verify before using this: each document's individual removal list must be a subset of the union **and** no document in the target set may legitimately keep a value that another document is removing. In luz-docs folder deletion this holds because every value in the union is a folder id that is being deleted tenant-wide — no surviving document is allowed to keep any of them.

Trade-off: on failure, audit/error reporting becomes batch-grained instead of per-document.

Related: [[MongoDB updateMany with pipeline $set + $filter is an order-preserving alternative to $pull]]

## Related

- [[MongoDB updateMany with pipeline $set + $filter is an order-preserving alternative to $pull]]
