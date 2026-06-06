---
title: "LUZ-155107 shipped as two commits so the inheritedSecurityClassCode fix can cherry-pick to earchive-master"
created: 2026-06-04
type: observation
status: seedling
source: "session 2026-06-04"
tags: [luz-docs, git, materialize, decision]
---

# LUZ-155107 shipped as two commits so the inheritedSecurityClassCode fix can cherry-pick to earchive-master

LUZ-155107 was deliberately shipped as TWO commits on branch `kepler/sprint-158/LUZ-155107-...-folder-recovery-investigation`, because the bug fix must be separable from the cascade feature.

- **Commit `471515af6`** — ONLY the folder-side fix: `FolderService.restoreToNewParentFolderIds` now re-derives `inheritedSecurityClassCode` for the recovered folder's subtree via `PutUpdatingSecurityClassFolderProcess` (same mechanism as the PUT update path). Kept `void`, with zero materialize plumbing.
- **Commit `3da9fe267`** — the materialize recovery cascade: `boolean reparented` plumbing, try/finally cascade hook in `recoverFolder`, the `MaterializeFolderRecovery*` event/service/marker files and tests.

**Why:** `earchive-master` does not contain the materialize cascade infrastructure. Keeping the fix commit free of any cascade plumbing means it can be cherry-picked into a standalone PR targeting `earchive-master` later without dragging the feature along.

Related: [[recoverFolder leaves inheritedSecurityClassCode stale on reparent]]

## Related

- [[recoverFolder leaves inheritedSecurityClassCode stale on reparent]]
