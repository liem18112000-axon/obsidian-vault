---
title: "FolderService.recoverFolder is not materialize-aware"
created: 2026-06-04
type: observation
status: budding
source: "LUZ-155107 investigation, session 2026-06-04"
tags: [luz-docs, materialize, folder-recovery, security, bug]
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
