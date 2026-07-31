---
ai_hash: fb830281dfac5046
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-04
entities:
- Folder recovery
- Inherited security
- Deletion statuses
- '`executeRecovery`'
- '`_deletionStatus`'
- '`restoreToNewParentFolderIds`'
- Mongo
- '`FolderNotFoundException`'
- '`RecoveryUpdatingSecurityClassFolderProcess`'
- '`run()`'
- '`inheritedSecurityClassCode`'
- LUZ-155136
- Recursive subtree walk
- Lifecycle flag
- getCollectionMetadataByTerms silently ignored includeDeletedRecord for non-document
  collections
- Put vs Patch UpdatingSecurityClassFolderProcess prefix denotes input shape, not
  logic
source: session 2026-06-04, FolderService.java
status: seedling
tags:
- luz-docs
- LUZ-155136
- ordering
- soft-delete
title: Folder recovery must recompute inherited security after deletion statuses are
  cleared
type: lesson
---

# Folder recovery must recompute inherited security after deletion statuses are cleared

In luz_docs folder recovery, the inherited-security recompute must run **after** `executeRecovery` clears `_deletionStatus` on the recovered subtree — not inside `restoreToNewParentFolderIds` (which runs first). Run too early and the recursive subtree walk breaks: each child computes its new inherited set by fetching its parents from Mongo, and a still-soft-deleted parent isn't returned → `FolderNotFoundException` → only the top folder gets updated, descendants stay stale (the LUZ-155136 follow-up bug).

The recompute is also **unconditional** on recovery (new `RecoveryUpdatingSecurityClassFolderProcess` calling `run()` directly instead of gating on a parentFolderIds diff), because parents' security classes may have changed while the folder sat in trash — re-parenting is not the only way inherited codes go stale. Idempotence comes from the equality skip check inside `run()` (treating a missing `inheritedSecurityClassCode` field as an empty set), which no-ops the write and the subtree walk when nothing changed.

**General lesson:** when a recompute depends on reads that are filtered by a lifecycle flag (soft-delete, draft, archived), sequence it after the step that flips the flag — or the recompute silently sees a partial world.

## Related
- [[getCollectionMetadataByTerms silently ignored includeDeletedRecord for non-document collections]]
- [[Put vs Patch UpdatingSecurityClassFolderProcess prefix denotes input shape, not logic]]

%% ai-graph-start %%

**Related notes:**
- [[Folder recovery re-parenting must recompute inheritedSecurityClassCode like the PUT path]]
- [[getCollectionMetadataByTerms silently ignored includeDeletedRecord for non-document collections]]
- [[FolderService.recoverFolder is not materialize-aware]]
- [[Folder recovery reuses the parent-change materialize cascade]]
- [[luz_docs folder security-class changes have 3 entry points but only PUT cascades]]

**Relations:**
- Folder recovery — *requires recomputing* — Inherited security
- Inherited security — *recompute must run after* — Deletion statuses
- `executeRecovery` — *clears* — `_deletionStatus`
- `restoreToNewParentFolderIds` — *runs before* — Inherited security recompute
- Running recompute too early — *breaks* — Recursive subtree walk
- Recursive subtree walk — *fetches parents from* — Mongo
- Still-soft-deleted parent — *is not returned by* — Mongo
- Not finding parents — *leads to* — `FolderNotFoundException`
- `FolderNotFoundException` — *results in* — only top folder updated
- Descendants — *remain stale due to* — `FolderNotFoundException`
- LUZ-155136 — *is a follow-up bug for* — stale descendants
- `RecoveryUpdatingSecurityClassFolderProcess` — *calls* — `run()`
- `run()` — *includes* — equality skip check
- Equality skip check — *treats missing* — `inheritedSecurityClassCode` as empty set
- Equality skip check — *prevents write and subtree walk when* — nothing changed
- Inherited security recompute — *is unconditional during* — recovery
- Parents' security classes — *can change while* — folder in trash
- Re-parenting — *is not the only cause of* — inherited codes going stale
- Inherited security recompute — *depends on reads filtered by* — Lifecycle flag
- Inherited security recompute — *should be sequenced after* — step that flips Lifecycle flag
- Folder recovery — *is related to* — getCollectionMetadataByTerms silently ignored includeDeletedRecord for non-document collections
- Folder recovery — *is related to* — Put vs Patch UpdatingSecurityClassFolderProcess prefix denotes input shape, not logic

%% ai-graph-end %%