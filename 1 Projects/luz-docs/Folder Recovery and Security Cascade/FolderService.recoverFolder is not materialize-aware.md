---
ai_hash: 1e562c0255112724
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-04
entities:
- FolderService.recoverFolder
- materialize-aware
- POST /{tenantId}/folders/{folder-id}/recovery
- JSON store
- MaterializeFacade
- FolderService
- rename path
- parent-change path
- restoreToNewParentFolderIds
- updateFolderMetadata
- inherited security
- path
- recoverSingleFolder
- deletionStatus
- deletionTimestamp
- executeRecovery
- subtree documents
- documentService.recoverDocument
- materialize stamping
- allowlisted tenants
- materialization-complete tenants
- _folderNames
- _effectiveSecurityClassCodes
- security-classification correctness risk
- docs
- security filter
- recovery-with-re-parenting
- Non-materialized tenants
- LUZ-155107
- sprint 158
- MaterializeFolderRecoveryService
- rename-style event
- marker
- PARTIAL retry
- marker collection
- materializeCascade
- parent-change full-recompute pipeline
- root
- descendants
- shouldUseMaterialized(tenantId)
- luz_docs has two materialize cascade delivery mechanisms
- Folder recovery re-parenting must recompute inheritedSecurityClassCode like the
  PUT path
- PUT path
source: LUZ-155107 investigation, session 2026-06-04
status: budding
tags:
- luz-docs
- materialize
- folder-recovery
- security
- bug
title: FolderService.recoverFolder is not materialize-aware
type: observation
---

# FolderService.recoverFolder is not materialize-aware

The folder recovery API (`POST /{tenantId}/folders/{folder-id}/recovery` → `FolderService.recoverFolder`) restores state by writing straight to the JSON store and never touches `MaterializeFacade` — even though FolderService injects it for the rename/parent-change paths.

The three write sites, none of which cascade:
- `restoreToNewParentFolderIds` — re-parents via raw `updateFolderMetadata` (changes inherited security + path!)
- `recoverSingleFolder` — flips `deletionStatus`/clears `deletionTimestamp`, raw write
- `executeRecovery` — recovers subtree documents via `documentService.recoverDocument` (no materialize stamping)

Consequence for allowlisted/materialization-complete tenants: the recovered subtree keeps stale `_folderNames` and stale `_effectiveSecurityClassCodes` — a security-classification correctness risk (docs surfaced under wrong security filter or wrongly hidden). Worst case is recovery-with-re-parenting. Non-materialized tenants are unaffected.

Fix (LUZ-155107, sprint 158): new async `MaterializeFolderRecoveryService` — rename-style event + marker + PARTIAL retry, but in its OWN marker collection (isolated from rename's `materializeCascade`), executing the parent-change full-recompute pipeline over root + ALL descendants; fires in finally even on partial recovery; gated by `shouldUseMaterialized(tenantId)`.

Related: [[luz_docs has two materialize cascade delivery mechanisms]], [[1 Projects/luz-docs/Folder Recovery and Security Cascade/Folder recovery re-parenting must recompute inheritedSecurityClassCode like the PUT path]]

## Related

- [[luz_docs has two materialize cascade delivery mechanisms]]
- [[1 Projects/luz-docs/Folder Recovery and Security Cascade/Folder recovery re-parenting must recompute inheritedSecurityClassCode like the PUT path]]

%% ai-graph-start %%

**Related notes:**
- [[Folder recovery re-parenting must recompute inheritedSecurityClassCode like the PUT path]]
- [[Folder recovery reuses the parent-change materialize cascade]]
- [[Folder recovery must recompute inherited security after deletion statuses are cleared]]
- [[luz_docs has two materialize cascade delivery mechanisms]]
- [[DocumentService.recoverDocument re-stamps materialized fields via the cascade diff]]

**Relations:**
- FolderService.recoverFolder — *is not* — materialize-aware
- POST /{tenantId}/folders/{folder-id}/recovery — *is* — FolderService.recoverFolder
- FolderService.recoverFolder — *writes to* — JSON store
- FolderService.recoverFolder — *does not touch* — MaterializeFacade
- FolderService — *injects* — MaterializeFacade
- MaterializeFacade — *is used for* — rename path
- MaterializeFacade — *is used for* — parent-change path
- restoreToNewParentFolderIds — *is a write site for* — FolderService.recoverFolder
- restoreToNewParentFolderIds — *re-parents via* — updateFolderMetadata
- updateFolderMetadata — *changes* — inherited security
- updateFolderMetadata — *changes* — path
- recoverSingleFolder — *is a write site for* — FolderService.recoverFolder
- recoverSingleFolder — *flips* — deletionStatus
- recoverSingleFolder — *clears* — deletionTimestamp
- executeRecovery — *is a write site for* — FolderService.recoverFolder
- executeRecovery — *recovers* — subtree documents
- executeRecovery — *uses* — documentService.recoverDocument
- documentService.recoverDocument — *lacks* — materialize stamping
- allowlisted tenants — *are affected by* — security-classification correctness risk
- materialization-complete tenants — *are affected by* — security-classification correctness risk
- recovered subtree — *keeps stale* — _folderNames
- recovered subtree — *keeps stale* — _effectiveSecurityClassCodes
- _effectiveSecurityClassCodes — *causes* — security-classification correctness risk
- security-classification correctness risk — *can lead to* — docs surfaced under wrong security filter
- security-classification correctness risk — *can lead to* — docs wrongly hidden
- recovery-with-re-parenting — *is the* — Worst case
- Non-materialized tenants — *are unaffected by* — security-classification correctness risk
- LUZ-155107 — *is a* — Fix
- Fix — *is planned for* — sprint 158
- Fix — *introduces* — MaterializeFolderRecoveryService
- MaterializeFolderRecoveryService — *is a* — new async service
- MaterializeFolderRecoveryService — *uses* — rename-style event
- MaterializeFolderRecoveryService — *uses* — marker
- MaterializeFolderRecoveryService — *uses* — PARTIAL retry
- MaterializeFolderRecoveryService — *uses* — marker collection
- marker collection — *is isolated from* — materializeCascade
- materializeCascade — *is for* — rename
- MaterializeFolderRecoveryService — *executes* — parent-change full-recompute pipeline
- parent-change full-recompute pipeline — *operates over* — root
- parent-change full-recompute pipeline — *operates over* — descendants
- MaterializeFolderRecoveryService — *fires* — in finally
- MaterializeFolderRecoveryService — *is gated by* — shouldUseMaterialized(tenantId)
- luz_docs has two materialize cascade delivery mechanisms — *is related to* — FolderService.recoverFolder is not materialize-aware
- Folder recovery re-parenting must recompute inheritedSecurityClassCode like the PUT path — *is related to* — FolderService.recoverFolder is not materialize-aware
- Folder recovery re-parenting must recompute inheritedSecurityClassCode like the PUT path — *mentions* — PUT path

%% ai-graph-end %%