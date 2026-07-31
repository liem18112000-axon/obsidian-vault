---
title: "MongoDB updateMany with pipeline $set + $filter is an order-preserving alternative to $pull"
created: 2026-06-10
type: howto
status: seedling
source: "luz-docs enhance-delete-folder-api, sprint 158 (2026-06-10)"
tags: [mongodb, updateMany, aggregation-pipeline, luz-docs]
---

# MongoDB updateMany with pipeline $set + $filter is an order-preserving alternative to $pull

When you need to strip a set of values from an array field across many documents in one MongoDB call, an aggregation-pipeline update — `updateMany(filter, [{ $set: { field: { $filter: { input: { $ifNull: ["$field", []] }, cond: { $not: [{ $in: ["$$this", <valuesToRemove>] }] } } } } }])` — removes the values while **preserving the original element order**, exactly like rewriting the array client-side.

Why not `$pull`: `$pull` also preserves order, but it cannot express conditions that reference other fields or compose with `$ifNull` for missing arrays, and pipeline-form updates let you set several computed fields in the same pass. The `$filter` form is the drop-in replacement when migrating per-document read-modify-write logic (load doc, rewrite array, save) into a single `updateMany` without changing the resulting array shape.

Used in luz-docs folder deletion (sprint 158) to strip deleted-folder ids from `folderIds` of all surviving documents in one call instead of N updates.

Related: [[Removing the union of array values per document is safe because absent values are no-ops]]

## Related

- [[Removing the union of array values per document is safe because absent values are no-ops]]
