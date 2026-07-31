---
ai_hash: eb1dc711e3b071e7
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-10
entities:
- luz-docs
- delete-folder batching roadmap
- kepler/sprint-158/enhance-delete-folder-api
- folder-deletion flow
- batched Mongo calls
- Mongo
- updateMany
- deleteMany
- folder-id stripping
- order-preserving pipeline
- count-based security-class fail-fast
- paged projected scans
- folderIds.1 exists
- Permanent-delete path for individual documents
- documentDeletingService.deleteDocument
- Folder-metadata updates for folders losing a deleted parent
- updateFolderHavingParentIsDeletedFolder
- Review of DELETE /folders/{folder-id} REST contract changes
- DELETE /folders/{folder-id}
- REST contract
- Liem
- MongoDB updateMany with pipeline $set + $filter is an order-preserving alternative
  to $pull
- Validate with a count query for violators instead of loading all documents
source: session 2026-06-10, user confirmation
status: seedling
tags:
- luz-docs
- sprint-158
- roadmap
- mongodb
title: luz-docs delete-folder batching roadmap - remaining per-item paths
type: observation
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

%% ai-graph-start %%

**Related notes:**
- [[Batched folder delete strips folder ids via union updateMany]]
- [[Folder-deletion batching lost to materialise cascade in sprint-158 merge]]
- [[Bulk write paths in folder delete only engage with more than one document]]
- [[luz-docs folder delete filter double-fetched every subfolder]]
- [[Removing the union of array values per document is safe because absent values are no-ops]]

**Relations:**
- luz-docs — *has* — delete-folder batching roadmap
- delete-folder batching roadmap — *is on branch* — kepler/sprint-158/enhance-delete-folder-api
- kepler/sprint-158/enhance-delete-folder-api — *is part of* — luz-docs
- folder-deletion flow — *is being converted to* — batched Mongo calls
- batched Mongo calls — *uses* — updateMany
- batched Mongo calls — *uses* — deleteMany
- batched Mongo calls — *includes* — folder-id stripping
- folder-id stripping — *uses* — order-preserving pipeline
- folder-id stripping — *uses* — updateMany
- batched Mongo calls — *includes* — count-based security-class fail-fast
- batched Mongo calls — *includes* — paged projected scans
- paged projected scans — *splits on* — folderIds.1 exists
- delete-folder batching roadmap — *includes remaining path* — Permanent-delete path for individual documents
- Permanent-delete path for individual documents — *uses* — documentDeletingService.deleteDocument
- delete-folder batching roadmap — *includes remaining path* — Folder-metadata updates for folders losing a deleted parent
- Folder-metadata updates for folders losing a deleted parent — *uses* — updateFolderHavingParentIsDeletedFolder
- delete-folder batching roadmap — *includes remaining path* — Review of DELETE /folders/{folder-id} REST contract changes
- Review of DELETE /folders/{folder-id} REST contract changes — *concerns* — DELETE /folders/{folder-id}
- DELETE /folders/{folder-id} — *is a* — REST contract
- Liem — *confirmed plan for* — Permanent-delete path for individual documents
- Liem — *confirmed plan for* — Folder-metadata updates for folders losing a deleted parent
- Liem — *confirmed plan for* — Review of DELETE /folders/{folder-id} REST contract changes
- delete-folder batching roadmap — *is related to* — MongoDB updateMany with pipeline $set + $filter is an order-preserving alternative to $pull
- delete-folder batching roadmap — *is related to* — Validate with a count query for violators instead of loading all documents

%% ai-graph-end %%