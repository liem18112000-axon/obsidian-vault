---
title: "Folder recovery must recompute inherited security after deletion statuses are cleared"
created: 2026-06-04
type: lesson
status: seedling
source: "session 2026-06-04, FolderService.java"
tags: [luz-docs, LUZ-155136, ordering, soft-delete]
---

# Folder recovery must recompute inherited security after deletion statuses are cleared

In luz_docs folder recovery, the inherited-security recompute must run **after** `executeRecovery` clears `_deletionStatus` on the recovered subtree — not inside `restoreToNewParentFolderIds` (which runs first). Run too early and the recursive subtree walk breaks: each child computes its new inherited set by fetching its parents from Mongo, and a still-soft-deleted parent isn't returned → `FolderNotFoundException` → only the top folder gets updated, descendants stay stale (the LUZ-155136 follow-up bug).

The recompute is also **unconditional** on recovery (new `RecoveryUpdatingSecurityClassFolderProcess` calling `run()` directly instead of gating on a parentFolderIds diff), because parents' security classes may have changed while the folder sat in trash — re-parenting is not the only way inherited codes go stale. Idempotence comes from the equality skip check inside `run()` (treating a missing `inheritedSecurityClassCode` field as an empty set), which no-ops the write and the subtree walk when nothing changed.

**General lesson:** when a recompute depends on reads that are filtered by a lifecycle flag (soft-delete, draft, archived), sequence it after the step that flips the flag — or the recompute silently sees a partial world.

## Related
- [[getCollectionMetadataByTerms silently ignored includeDeletedRecord for non-document collections]]
- [[Put vs Patch UpdatingSecurityClassFolderProcess prefix denotes input shape, not logic]]
