---
title: "Split bulk scans on folderIds.1 exists to separate single-array-element fast path"
created: 2026-06-10
type: howto
status: seedling
source: "luz-docs enhance-delete-folder-api, sprint 158 (2026-06-10)"
tags: [mongodb, projection, performance, pagination, luz-docs]
---

# Split bulk scans on folderIds.1 exists to separate single-array-element fast path

In MongoDB, `{"arrayField.1": {$exists: true}}` matches documents whose array has **two or more elements** — a cheap way to partition a scan into a bulk fast path and an expensive per-item path.

Pattern from luz-docs folder deletion: documents that live only in the folder being deleted (`folderIds == [folderId]`, i.e. `folderIds.1` does NOT exist) need no per-document logic, so they are collected with a paged, `_id`-only-projection query straight into the deletable list. Only documents that also belong to other folders (`folderIds.1` exists) are fetched with a slim multi-field projection and run through per-document filtering. The common case moves at bulk speed; the per-item cost is paid only where the decision actually depends on document content.

General shape: when per-item processing exists only to handle a minority case, find a queryable predicate (`field.1 exists`, a flag, a type) that isolates that minority and stream the rest through a projection-trimmed bulk path.

Related: [[Validate with a count query for violators instead of loading all documents]]

## Related

- [[Validate with a count query for violators instead of loading all documents]]
