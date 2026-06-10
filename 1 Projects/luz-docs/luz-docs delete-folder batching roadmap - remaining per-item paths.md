---
title: "luz-docs delete-folder batching roadmap - remaining per-item paths"
created: 2026-06-10
type: observation
status: seedling
source: "session 2026-06-10, user confirmation"
tags: [luz-docs, sprint-158, roadmap, mongodb]
---

# luz-docs delete-folder batching roadmap - remaining per-item paths

On branch `kepler/sprint-158/enhance-delete-folder-api` (luz-docs), the folder-deletion flow is being converted from per-document round trips to batched Mongo calls. Done so far: soft-delete via one `updateMany` + one large-file `deleteMany`; folder-id stripping via one order-preserving pipeline `updateMany`; count-based security-class fail-fast; paged projected scans split on `folderIds.1 exists`.

Still per-item and planned for the next commits on this branch (confirmed by Liem, 2026-06-10):
1. Permanent-delete path for individual documents (`documentDeletingService.deleteDocument`) — still one call per document.
2. Folder-metadata updates for folders losing a deleted parent (`updateFolderHavingParentIsDeletedFolder`) — still one call per folder.
3. Review whether the `DELETE /folders/{folder-id}` REST contract needs changes for the batched flow (unchanged so far).

Related: [[MongoDB updateMany with pipeline $set + $filter is an order-preserving alternative to $pull]], [[Validate with a count query for violators instead of loading all documents]]

## Related

- [[MongoDB updateMany with pipeline $set + $filter is an order-preserving alternative to $pull]]
- [[Validate with a count query for violators instead of loading all documents]]
