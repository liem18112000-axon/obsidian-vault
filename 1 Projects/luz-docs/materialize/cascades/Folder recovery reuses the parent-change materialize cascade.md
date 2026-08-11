---
ai_hash: 7bc2e2abf0d03eff
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-04
entities:
- luz_docs folder recovery
- POST /{tenantId}/folders/{id}/recovery
- MaterializeFacade.onFolderParentChange
- parent-change cascade
- materialized security sentinels
- folder names
- subtree
- trash
- rename cascade filter
- documents
- folderIds
- deletionStatus exclusion
- soft-deleted documents
- _folderNames
- security sentinels
- _folderSecurityClassCodes
- _effectiveSecurityClassCodes
- _isPublic
- parent-change pipeline
- recomputeInheritedSecurityClassCodes
- folders collection
- folder docs
- PUT path
- pre/post security-union compare
- recovery
- shouldUseMaterialized(tenantId)
- drift accumulated in trash
- FETCH_HEAD is volatile when an IDE auto-fetches
source: session 2026-06-04 LUZ-155107
status: seedling
tags:
- luz-docs
- earchive
- materialize
- design-decision
title: Folder recovery reuses the parent-change materialize cascade
type: model
---

# Folder recovery reuses the parent-change materialize cascade

luz_docs folder recovery (POST /{tenantId}/folders/{id}/recovery) re-stamps materialized security sentinels by reusing the existing parent-change cascade (`MaterializeFacade.onFolderParentChange`) rather than adding a recovery-specific cascade.

**Why it suffices:** folder *names* cannot drift while a subtree sits in trash — the rename cascade filter matches documents by `folderIds` only (no deletionStatus exclusion), so soft-deleted documents keep `_folderNames` in sync. Only the security sentinels (`_folderSecurityClassCodes`, `_effectiveSecurityClassCodes`, `_isPublic`) can drift (re-parenting via restore body, or parent security changes during trash), and that is exactly what the parent-change pipeline recomputes.

**Ordering constraint:** the cascade must run AFTER `recomputeInheritedSecurityClassCodes` — it prefetches each folder's (own ∪ inherited) code union from the folders collection, so stale folder docs would re-stamp stale unions.

**Gating difference vs PUT:** the PUT path diff-gates on pre/post security-union compare; recovery always cascades when `shouldUseMaterialized(tenantId)` — drift accumulated in trash is not cheaply diffable.

Related: [[FETCH_HEAD is volatile when an IDE auto-fetches]]

%% ai-graph-start %%

**Related notes:**
- [[FolderService.recoverFolder is not materialize-aware]]
- [[Folder recovery must recompute inherited security after deletion statuses are cleared]]
- [[luz_docs has two materialize cascade delivery mechanisms]]
- [[Folder recovery re-parenting must recompute inheritedSecurityClassCode like the PUT path]]
- [[luz_docs folder security-class changes have 3 entry points but only PUT cascades]]

**Relations:**
- luz_docs folder recovery — *reuses* — parent-change cascade
- luz_docs folder recovery — *is* — POST /{tenantId}/folders/{id}/recovery
- parent-change cascade — *is* — MaterializeFacade.onFolderParentChange
- parent-change cascade — *re-stamps* — materialized security sentinels
- folder names — *cannot drift while* — subtree sits in trash
- rename cascade filter — *matches documents by* — folderIds
- rename cascade filter — *has no* — deletionStatus exclusion
- soft-deleted documents — *keep* — _folderNames
- security sentinels — *can drift* — 
- security sentinels — *include* — _folderSecurityClassCodes
- security sentinels — *include* — _effectiveSecurityClassCodes
- security sentinels — *include* — _isPublic
- parent-change pipeline — *recomputes* — security sentinels
- parent-change cascade — *must run AFTER* — recomputeInheritedSecurityClassCodes
- recomputeInheritedSecurityClassCodes — *prefetches code union from* — folders collection
- stale folder docs — *would re-stamp* — stale unions
- PUT path — *diff-gates on* — pre/post security-union compare
- recovery — *always cascades when* — shouldUseMaterialized(tenantId)
- drift accumulated in trash — *is not* — cheaply diffable
- luz_docs folder recovery — *related to* — FETCH_HEAD is volatile when an IDE auto-fetches

%% ai-graph-end %%