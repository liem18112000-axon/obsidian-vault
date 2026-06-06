---
title: "luz_docs has two materialize cascade delivery mechanisms"
created: 2026-06-04
type: concept
status: budding
source: "LUZ-155107 investigation, session 2026-06-04"
tags: [luz-docs, materialize, cascade, architecture]
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
