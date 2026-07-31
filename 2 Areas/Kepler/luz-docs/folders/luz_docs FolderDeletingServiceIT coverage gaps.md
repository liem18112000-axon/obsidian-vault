---
title: "luz_docs FolderDeletingServiceIT coverage gaps"
created: 2026-06-05
type: observation
status: seedling
source: "FolderDeletingServiceIT.java review, session 2026-06-05"
tags: [luz-docs, folders, delete, testing, coverage]
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
