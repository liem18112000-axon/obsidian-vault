---
ai_hash: 4c46ae0aff2e5e60
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-04
entities:
- LUZ-155107
- inheritedSecurityClassCode
- earchive-master
- kepler/sprint-158/LUZ-155107-...-folder-recovery-investigation
- 471515af6
- 3da9fe267
- FolderService.restoreToNewParentFolderIds
- PutUpdatingSecurityClassFolderProcess
- materialize recovery cascade
- recoverFolder
- MaterializeFolderRecovery*
- recoverFolder leaves inheritedSecurityClassCode stale on reparent
- folder-side fix
- materialize cascade infrastructure
source: session 2026-06-04
status: seedling
tags:
- luz-docs
- git
- materialize
- decision
title: LUZ-155107 shipped as two commits so the inheritedSecurityClassCode fix can
  cherry-pick to earchive-master
type: observation
---

# LUZ-155107 shipped as two commits so the inheritedSecurityClassCode fix can cherry-pick to earchive-master

LUZ-155107 was deliberately shipped as TWO commits on branch `kepler/sprint-158/LUZ-155107-...-folder-recovery-investigation`, because the bug fix must be separable from the cascade feature.

- **Commit `471515af6`** — ONLY the folder-side fix: `FolderService.restoreToNewParentFolderIds` now re-derives `inheritedSecurityClassCode` for the recovered folder's subtree via `PutUpdatingSecurityClassFolderProcess` (same mechanism as the PUT update path). Kept `void`, with zero materialize plumbing.
- **Commit `3da9fe267`** — the materialize recovery cascade: `boolean reparented` plumbing, try/finally cascade hook in `recoverFolder`, the `MaterializeFolderRecovery*` event/service/marker files and tests.

**Why:** `earchive-master` does not contain the materialize cascade infrastructure. Keeping the fix commit free of any cascade plumbing means it can be cherry-picked into a standalone PR targeting `earchive-master` later without dragging the feature along.

Related: [[recoverFolder leaves inheritedSecurityClassCode stale on reparent]]

## Related

- [[recoverFolder leaves inheritedSecurityClassCode stale on reparent]]

%% ai-graph-start %%

**Related notes:**
- [[Folder recovery re-parenting must recompute inheritedSecurityClassCode like the PUT path]]
- [[luz_docs parent-change cascade recovers forward, not via snapshot rollback]]
- [[Folder recovery must recompute inherited security after deletion statuses are cleared]]
- [[FolderService.recoverFolder is not materialize-aware]]
- [[Folder-deletion batching lost to materialise cascade in sprint-158 merge]]

**Relations:**
- LUZ-155107 — *shipped_as* — two commits
- LUZ-155107 — *on_branch* — kepler/sprint-158/LUZ-155107-...-folder-recovery-investigation
- inheritedSecurityClassCode — *is_a_fix_for* — LUZ-155107
- inheritedSecurityClassCode fix — *can_cherry_pick_to* — earchive-master
- 471515af6 — *is_commit_for* — LUZ-155107
- 471515af6 — *contains* — folder-side fix
- folder-side fix — *involves* — FolderService.restoreToNewParentFolderIds
- FolderService.restoreToNewParentFolderIds — *re_derives* — inheritedSecurityClassCode
- inheritedSecurityClassCode — *re_derived_via* — PutUpdatingSecurityClassFolderProcess
- 3da9fe267 — *is_commit_for* — LUZ-155107
- 3da9fe267 — *contains* — materialize recovery cascade
- materialize recovery cascade — *involves* — recoverFolder
- materialize recovery cascade — *involves* — MaterializeFolderRecovery*
- earchive-master — *does_not_contain* — materialize cascade infrastructure
- 471515af6 — *can_be_cherry_picked_to* — earchive-master
- LUZ-155107 — *related_to* — recoverFolder leaves inheritedSecurityClassCode stale on reparent
- inheritedSecurityClassCode fix — *addresses* — recoverFolder leaves inheritedSecurityClassCode stale on reparent

%% ai-graph-end %%