---
ai_hash: 97d759a496f4046a
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-05
entities: []
source: FolderDeletingServiceIT.java review, session 2026-06-05
status: seedling
tags:
- luz-docs
- folders
- delete
- testing
- coverage
title: luz_docs FolderDeletingServiceIT coverage gaps
type: observation
---

# luz_docs FolderDeletingServiceIT coverage gaps

As of 2026-06-05 (branch kepler/sprint-156/earchive-master), `FolderDeletingServiceIT` covers the happy paths and basic guards of the delete-folder API: empty folder ± parent, deletable docs, doc-update path, detail-response failure, parent mismatch/required, id-not-found, and the deletion-status state-machine guards.

**Not covered** (test-gap backlog for the delete-folder API):
- Legacy folders with no `deletionStatus` field (pass both state-machine checks silently)
- Soft delete of a doc shared only with already-trashed folders
- Doc dedup when reachable via two deleted folders; diamond folder structures
- Trash sub-folder inclusion/exclusion per soft vs permanent walk; paging beyond `SEARCH_PAGE`
- Security-class rejection from a locked item deep in the tree
- Non-transactional partial failure + `_versionNumber` concurrency race
- Materialize cascade assertions (`_folderNames` / `_effectiveSecurityClassCodes` sentinels) — the eArchive-critical gap
- Races with `DeleteFolderAndDocumentJob` / scheduled `DeleteFoldersJob`

Full case matrix exported locally to `delete-folder-api-critical-cases.md` in the liem_luz_docs repo root (local report — not committed).

## Related

- [[luz_docs delete folder API soft vs permanent state machine]]
- [[luz_docs deleteFolder isDetailResponse error contract and non-transactionality]]

%% ai-graph-start %%

**Related notes:**
- [[luz_docs deleteFolder isDetailResponse error contract and non-transactionality]]
- [[Folder-deletion batching lost to materialise cascade in sprint-158 merge]]
- [[Luz delete-folder tests can only delete public folders, not ones carrying a security class]]
- [[Bulk write paths in folder delete only engage with more than one document]]
- [[luz-docs delete-folder batching roadmap - remaining per-item paths]]

%% ai-graph-end %%