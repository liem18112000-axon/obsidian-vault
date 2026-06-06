---
title: "Folder recovery reuses the parent-change materialize cascade"
created: 2026-06-04
type: model
status: seedling
source: "session 2026-06-04 LUZ-155107"
tags: [luz-docs, earchive, materialize, design-decision]
---

# Folder recovery reuses the parent-change materialize cascade

luz_docs folder recovery (POST /{tenantId}/folders/{id}/recovery) re-stamps materialized security sentinels by reusing the existing parent-change cascade (`MaterializeFacade.onFolderParentChange`) rather than adding a recovery-specific cascade.

**Why it suffices:** folder *names* cannot drift while a subtree sits in trash — the rename cascade filter matches documents by `folderIds` only (no deletionStatus exclusion), so soft-deleted documents keep `_folderNames` in sync. Only the security sentinels (`_folderSecurityClassCodes`, `_effectiveSecurityClassCodes`, `_isPublic`) can drift (re-parenting via restore body, or parent security changes during trash), and that is exactly what the parent-change pipeline recomputes.

**Ordering constraint:** the cascade must run AFTER `recomputeInheritedSecurityClassCodes` — it prefetches each folder's (own ∪ inherited) code union from the folders collection, so stale folder docs would re-stamp stale unions.

**Gating difference vs PUT:** the PUT path diff-gates on pre/post security-union compare; recovery always cascades when `shouldUseMaterialized(tenantId)` — drift accumulated in trash is not cheaply diffable.

Related: [[FETCH_HEAD is volatile when an IDE auto-fetches]]
