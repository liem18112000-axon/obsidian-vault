---
ai_hash: 8f69ad27edc2b2e9
ai_model: google/gemini-2.5-flash
ai_updated: '2026-05-30'
entities:
- Retry Flow
- Retry
- Tenant traffic
- Scheduled job
- MaterializeRequestFilter
- MaterializeEvent
- MaterializeRetryEvent
- onCascadeRetry
- repository
- readPartialCascadeMarkers
- CASCADE_RETRY_BATCH
- Marker
- PARTIAL
- Folder
- loadFolderName
- claimCascadeMarker
- START
- MaterializeFolderRenameEvent
- Observer
- onFolderRename
- HTTP 207
- Allowlisted request
- Materialized behavior
- Batch
- Parasitic on traffic
- Duplicate row
- Request path
- Cascade
- Folder-wide work
- failed work
- background work
- async event
- Code
---

# Retry Flow

[[folder-rename-cascade|Back to index]] | Previous: [[04 Marker State Machine|Marker state machine]] | Next: [[06 Files of Record|files of record]]

Retry is parasitic on regular tenant traffic. That means there is no separate scheduled job. Instead, normal requests for allowlisted tenants also publish a retry event in the background.

![[diagrams/04-retry-flow.png]]

## What happens on each allowlisted request

Every request that goes through `MaterializeRequestFilter` for an allowlisted tenant fires:

1. The usual `MaterializeEvent`.
2. A `MaterializeRetryEvent.fireAsync(...)`.

The request path only pays the cost of publishing an async event.

## Inside `onCascadeRetry`

1. **Read** - `repository.readPartialCascadeMarkers(tenantId, token, CASCADE_RETRY_BATCH)` returns up to `CASCADE_RETRY_BATCH` rows where `status == PARTIAL`.
2. **For each marker**:
   - Resolve the folder's current name with `repository.loadFolderName(tenantId, token, folderId)`.
   - If the folder is gone, delete the marker because there is nothing left to cascade.
   - Otherwise, `claimCascadeMarker(markerId)` flips the row back to `START` so another retry skips it.
   - Fire `MaterializeFolderRenameEvent(tenantId, folderId, newName, markerId, token)`.
3. **Observer reuses the marker** - `onFolderRename` sees `event.markerId() != null` and uses that existing marker instead of creating a duplicate row.

If the retry also ends in HTTP 207, the marker goes back to `PARTIAL` and waits for the next allowlisted request.

There is no maximum retry count. The marker remains until the cascade fully applies or the folder is deleted.

## Important caution

`CASCADE_RETRY_BATCH` is currently `1`, meaning one stuck folder is retried per allowlisted request. Increasing it can clear stuck folders faster, but every claimed row represents folder-wide work happening in the background.

## Beginner terms

| Term | Beginner explanation |
|---|---|
| Retry | Trying failed or unfinished work again later. |
| Batch | A group of items processed together. Here the batch size is one marker. |
| Parasitic on traffic | Work runs only when normal user/API traffic happens, instead of being run by a scheduled job. |
| Scheduled job | Background work that runs on a timer, like every minute. |
| Duplicate row | Two database records representing the same work. The code avoids creating these during retry. |
| Allowlisted request | A request from a tenant for which materialized behavior is enabled. |

%% ai-graph-start %%

**Related notes:**
- [[04 Marker State Machine]]
- [[03 Cascade Attempt]]
- [[07 Operational Notes]]
- [[folder-rename-cascade]]
- [[02 Trigger Flow]]

**Relations:**
- Retry Flow — *describes* — Retry
- Retry — *is parasitic on* — Tenant traffic
- Retry — *does not use* — Scheduled job
- Allowlisted request — *publishes* — MaterializeRetryEvent
- Allowlisted request — *goes through* — MaterializeRequestFilter
- MaterializeRequestFilter — *fires* — MaterializeEvent
- MaterializeRequestFilter — *fires* — MaterializeRetryEvent
- Request path — *publishes* — MaterializeRetryEvent
- onCascadeRetry — *calls* — readPartialCascadeMarkers
- readPartialCascadeMarkers — *returns* — Marker
- Marker — *has status* — PARTIAL
- readPartialCascadeMarkers — *uses* — CASCADE_RETRY_BATCH
- Marker — *associated with* — Folder
- onCascadeRetry — *calls* — loadFolderName
- loadFolderName — *resolves name for* — Folder
- Folder — *deletion causes deletion of* — Marker
- onCascadeRetry — *calls* — claimCascadeMarker
- claimCascadeMarker — *changes status of* — Marker
- Marker — *status changes to* — START
- onCascadeRetry — *fires* — MaterializeFolderRenameEvent
- Observer — *reuses* — Marker
- onFolderRename — *uses* — Marker
- Retry — *ends in* — HTTP 207
- HTTP 207 — *sets Marker status to* — PARTIAL
- Marker — *persists until* — Cascade
- Marker — *persists until* — Folder
- CASCADE_RETRY_BATCH — *is* — 1
- CASCADE_RETRY_BATCH — *controls* — Folder
- Folder — *retried per* — Allowlisted request
- Marker — *represents* — Folder-wide work
- Retry — *is* — trying failed work again
- Batch — *is a group of* — items
- Batch — *size is* — one Marker
- Parasitic on traffic — *means work runs with* — Tenant traffic
- Parasitic on traffic — *is alternative to* — Scheduled job
- Scheduled job — *is* — background work
- Code — *avoids* — Duplicate row
- Allowlisted request — *is from* — Tenant
- Allowlisted request — *enables* — Materialized behavior
- MaterializeRetryEvent — *is a type of* — async event
- repository — *provides* — readPartialCascadeMarkers
- repository — *provides* — loadFolderName
- repository — *provides* — claimCascadeMarker

%% ai-graph-end %%