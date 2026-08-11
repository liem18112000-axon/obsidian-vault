---
ai_hash: be81c523bd6a7cb7
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-10
entities:
- luz-docs
- FolderUtil.filterDeleteFolder
- getSubFolders
- getFolderMetadata
- getFolderMetadataById
- filterSubFolder
- getFolderById
- jsonstore
- Mongo
- sprint-158
- enhance-delete-folder-api branch
- JsonObject-based overload
- String-ID variant
- dedup contains(folderId) guard
- Pass fetched objects down recursion instead of IDs to avoid N+1 re-fetch
- child ID
- subFolder.asJsonObject()
source: session 2026-06-10, FolderUtil.java
status: seedling
tags:
- luz-docs
- mongodb
- performance
- n-plus-one
title: luz-docs folder delete filter double-fetched every subfolder
type: lesson
---

# luz-docs folder delete filter double-fetched every subfolder

In luz-docs, `FolderUtil.filterDeleteFolder` (the recursive filter behind folder deletion) fetched every folder in the tree twice: `getSubFolders` already returns the FULL metadata of each child (its `getFolderMetadata` call uses a null projection, so the shape equals `getFolderMetadataById`), yet `filterSubFolder` passed only the child ID back into the recursion, which immediately re-fetched it via `getFolderById` — one redundant jsonstore/Mongo round-trip per folder except the root.

Fix (sprint-158, enhance-delete-folder-api branch): a private `JsonObject`-based overload processes an already-fetched folder; the String-ID variant stays as the thin entry point doing dedup-check → fetch → delegate. `filterSubFolder` now passes `subFolder.asJsonObject()` directly.

The dedup `contains(folderId)` guard is intentionally duplicated in both overloads so the ID path can bail before the fetch.

General pattern: [[Pass fetched objects down recursion instead of IDs to avoid N+1 re-fetch]]

## Related

- [[Pass fetched objects down recursion instead of IDs to avoid N+1 re-fetch]]

%% ai-graph-start %%

**Related notes:**
- [[Pass fetched objects down recursion instead of IDs to avoid N+1 re-fetch]]
- [[Batched folder delete strips folder ids via union updateMany]]
- [[luz-docs delete-folder batching roadmap - remaining per-item paths]]
- [[luz-docs folder delete verifies document security classes with one limit-1 Mongo query per folder]]
- [[luz_docs folder delete multi-parent semantics]]

**Relations:**
- FolderUtil.filterDeleteFolder — *is_in* — luz-docs
- FolderUtil.filterDeleteFolder — *uses* — getSubFolders
- FolderUtil.filterDeleteFolder — *uses* — filterSubFolder
- getSubFolders — *returns* — FULL metadata
- getSubFolders — *calls* — getFolderMetadata
- getFolderMetadata — *uses* — null projection
- getFolderMetadataById — *defines_shape_for* — getFolderMetadata
- filterSubFolder — *previously_passed* — child ID
- child ID — *caused_re-fetch_via* — getFolderById
- getFolderById — *causes_round_trip_to* — jsonstore
- getFolderById — *causes_round_trip_to* — Mongo
- JsonObject-based overload — *introduced_in* — sprint-158
- JsonObject-based overload — *introduced_in_branch* — enhance-delete-folder-api branch
- JsonObject-based overload — *processes* — already-fetched folder
- String-ID variant — *is_an* — entry point
- String-ID variant — *performs* — dedup-check
- String-ID variant — *performs* — fetch
- String-ID variant — *performs* — delegate
- filterSubFolder — *now_passes* — subFolder.asJsonObject()
- dedup contains(folderId) guard — *is_duplicated_in* — both overloads
- dedup contains(folderId) guard — *enables* — bail before fetch
- Pass fetched objects down recursion instead of IDs to avoid N+1 re-fetch — *is_a* — General pattern
- Pass fetched objects down recursion instead of IDs to avoid N+1 re-fetch — *is_related_to_fix_for* — FolderUtil.filterDeleteFolder
- Pass fetched objects down recursion instead of IDs to avoid N+1 re-fetch — *is_implemented_by* — JsonObject-based overload
- Pass fetched objects down recursion instead of IDs to avoid N+1 re-fetch — *is_implemented_by* — String-ID variant

%% ai-graph-end %%