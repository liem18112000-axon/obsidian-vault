---
title: "luz-docs folder delete filter double-fetched every subfolder"
created: 2026-06-10
type: lesson
status: seedling
source: "session 2026-06-10, FolderUtil.java"
tags: [luz-docs, mongodb, performance, n-plus-one]
---

# luz-docs folder delete filter double-fetched every subfolder

In luz-docs, `FolderUtil.filterDeleteFolder` (the recursive filter behind folder deletion) fetched every folder in the tree twice: `getSubFolders` already returns the FULL metadata of each child (its `getFolderMetadata` call uses a null projection, so the shape equals `getFolderMetadataById`), yet `filterSubFolder` passed only the child ID back into the recursion, which immediately re-fetched it via `getFolderById` — one redundant jsonstore/Mongo round-trip per folder except the root.

Fix (sprint-158, enhance-delete-folder-api branch): a private `JsonObject`-based overload processes an already-fetched folder; the String-ID variant stays as the thin entry point doing dedup-check → fetch → delegate. `filterSubFolder` now passes `subFolder.asJsonObject()` directly.

The dedup `contains(folderId)` guard is intentionally duplicated in both overloads so the ID path can bail before the fetch.

General pattern: [[Pass fetched objects down recursion instead of IDs to avoid N+1 re-fetch]]

## Related

- [[Pass fetched objects down recursion instead of IDs to avoid N+1 re-fetch]]
