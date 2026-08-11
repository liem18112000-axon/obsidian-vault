---
ai_hash: fe7832861e24f240
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-04
entities: []
source: LUZ-155107 investigation, session 2026-06-04
status: budding
tags:
- luz-docs
- materialize
- cascade
- architecture
title: luz_docs has two materialize cascade delivery mechanisms
type: concept
---

# luz_docs has two materialize cascade delivery mechanisms

luz_docs ships two distinct delivery mechanisms for keeping the materialized document snapshot (`_folderNames`, `_folderSecurityClassCodes`, `_effectiveSecurityClassCodes`, `_isPublic`) in sync after a folder change — they have different consistency models:

1. **Folder rename — async + marker + PARTIAL retry** (`MaterializeFolderRenameService`): fire an async CDI event, insert a `materializeCascade` marker row (status START), run one bulk `updateManyByFilter`. Full apply → delete marker; HTTP 207 multi-status → mark PARTIAL. `MaterializeRequestFilter` fires a retry event on every request for allowlisted tenants; the sweep drains PARTIAL markers (batch=1), re-deriving the new name from current folder state. Eventually consistent, never blocks the API response.

2. **Folder parent change — synchronous snapshot + rollback** (`MaterializeFolderParentChangeService`): snapshot the 4 sentinels of every affected doc into `materializeCascadeSnapshot`, run the full $lookup recompute pipeline with @Retry, on exhausted retries restore from snapshot (rollback). Failed rollbacks get status RESTORE_PENDING and are swept later. Blocks the API call; all-or-nothing semantics.

Key asymmetry to remember: the parent-change pipeline (`buildFolderParentChangePipeline`) is a *full idempotent recompute* of all 4 sentinels from current folder state via $lookup — reusable by any cascade that needs 'recompute everything for docs in these folders' (e.g. folder recovery). The rename pipeline only patches the one name slot.

Gotcha: both cascade services need a self-injected reference for @Retry/@Fallback to apply (CDI proxy bypass on this.method() calls).

Related: [[FolderService.recoverFolder is not materialize-aware]]

## Related

- [[FolderService.recoverFolder is not materialize-aware]]

%% ai-graph-start %%

**Related notes:**
- [[Folder recovery reuses the parent-change materialize cascade]]
- [[luz_docs materialize passive retry via cascade markers]]
- [[FolderService.recoverFolder is not materialize-aware]]
- [[luz_docs onFolderParentsChange risk profile - sync fan-out, page-read gap, paging races]]
- [[Materialize folder parentFolderIds change cascade (LUZ-154159)]]

%% ai-graph-end %%