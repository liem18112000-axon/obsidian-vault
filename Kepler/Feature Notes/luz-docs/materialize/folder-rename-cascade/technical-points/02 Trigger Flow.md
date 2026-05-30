---
ai_hash: 1c8c3ac8db87cc02
ai_model: google/gemini-2.5-flash
ai_updated: '2026-05-30'
entities:
- Trigger Flow
- folder-rename-cascade
- 01 Overview - Folder Rename Cascade
- 03 Cascade Attempt
- rename hook
- folder PUT
- folder PATCH
- request thread
- async CDI observer
- real cascade work
- diagrams/01-trigger-flow.png
- ch.klara.luz.docs.materialize
- HTTP layer
- FolderService
- FolderService.cascadeFolderRenameIfNeeded
- MaterializeFacade.onFolderNameChange
- tenant allowlist
- shouldUseMaterialized
- MaterializeFolderRenameService.onFolderNameChange
- folderRenameEvent.fireAsync
- ManagedExecutorService
- NotificationOptions.ofExecutor(executor)
- MaterializeFolderRenameService.onFolderRename
- cascade
- API request
- original implementation
- folder
- 27k+ documents
- new MongoDB pipeline
- async boundary
- API response time
- MongoDB update latency
- PUT
- PATCH
- Hook
- CDI observer
- Async observer
- Allowlist
- Java/Jakarta
- event
---

# Trigger Flow

[[folder-rename-cascade|Back to index]] | Previous: [[01 Overview - Folder Rename Cascade|Overview]] | Next: [[03 Cascade Attempt|Cascade attempt]]

The rename hook fires on both folder `PUT` and `PATCH` paths. It starts on the normal request thread, then hands the real cascade work to an async CDI observer.

![[diagrams/01-trigger-flow.png]]

## Call chain

Every step is in `ch.klara.luz.docs.materialize` unless noted.

| # | Caller | Method | Notes |
|---|---|---|---|
| 1 | HTTP layer | `PUT /folders/{id}` / `PATCH /folders/{id}` | Both flows go through `FolderService`. |
| 2 | `FolderService.cascadeFolderRenameIfNeeded` | line 501 | Guard: `shouldCascadeFolderRename(currentName, newName)` must be true. |
| 3 | `MaterializeFacade.onFolderNameChange` | - | Checks the tenant allowlist with `shouldUseMaterialized`. |
| 4 | `MaterializeFolderRenameService.onFolderNameChange` | - | Thin try/catch around `fireAsync`. |
| 5 | `folderRenameEvent.fireAsync(event, NotificationOptions.ofExecutor(executor))` | - | Hands work to the `ManagedExecutorService`; the request thread can return immediately. |
| 6 | `MaterializeFolderRenameService.onFolderRename(@ObservesAsync ...)` | - | The cascade actually runs here. |

## Why async?

**Async** means "do this later or in the background instead of making the current request wait."

The original implementation could block the API request for about 7 hours when a folder had 27k+ documents. The new MongoDB pipeline is much faster, but the async boundary remains so API response time does not depend directly on MongoDB update latency.

## Beginner terms

| Term | Beginner explanation |
|---|---|
| HTTP layer | The part of the app that receives API calls like `PUT /folders/{id}`. |
| PUT | Usually means "replace/update this resource." |
| PATCH | Usually means "partially update this resource." |
| Request thread | The worker currently handling the user's API request. If it is blocked, the user waits. |
| Hook | Code that runs automatically when a certain event happens. Here: after a folder rename is detected. |
| CDI observer | Java/Jakarta mechanism where one part of the app publishes an event and another part reacts to it. |
| Async observer | An observer that reacts in the background instead of blocking the publisher. |
| Allowlist | A list of tenants for which this feature is enabled. |

%% ai-graph-start %%

**Related notes:**
- [[03 Cascade Attempt]]
- [[folder-rename-cascade]]
- [[05 Retry Flow]]
- [[01 Overview - Folder Rename Cascade]]
- [[07 Operational Notes]]

**Relations:**
- Trigger Flow — *back to* — folder-rename-cascade
- Trigger Flow — *previous is* — 01 Overview - Folder Rename Cascade
- Trigger Flow — *next is* — 03 Cascade Attempt
- rename hook — *fires on* — folder PUT
- rename hook — *fires on* — folder PATCH
- rename hook — *starts on* — request thread
- rename hook — *hands work to* — async CDI observer
- async CDI observer — *performs* — real cascade work
- Call chain — *steps in package* — ch.klara.luz.docs.materialize
- HTTP layer — *handles* — folder PUT
- HTTP layer — *handles* — folder PATCH
- HTTP layer — *flows through* — FolderService
- FolderService.cascadeFolderRenameIfNeeded — *is called by* — FolderService
- FolderService.cascadeFolderRenameIfNeeded — *calls* — MaterializeFacade.onFolderNameChange
- MaterializeFacade.onFolderNameChange — *checks* — tenant allowlist
- MaterializeFacade.onFolderNameChange — *uses* — shouldUseMaterialized
- MaterializeFacade.onFolderNameChange — *calls* — MaterializeFolderRenameService.onFolderNameChange
- MaterializeFolderRenameService.onFolderNameChange — *wraps* — folderRenameEvent.fireAsync
- folderRenameEvent.fireAsync — *hands work to* — ManagedExecutorService
- folderRenameEvent.fireAsync — *uses* — NotificationOptions.ofExecutor(executor)
- request thread — *can return immediately after* — folderRenameEvent.fireAsync
- MaterializeFolderRenameService.onFolderRename — *runs* — cascade
- MaterializeFolderRenameService.onFolderRename — *is an* — Async observer
- Async — *means* — do this later
- Async — *means* — do this in the background
- original implementation — *could block* — API request
- original implementation — *blocked for* — 7 hours
- original implementation — *involved folder with* — 27k+ documents
- new MongoDB pipeline — *is* — much faster
- async boundary — *remains for* — API response time
- API response time — *does not depend on* — MongoDB update latency
- HTTP layer — *is part of* — app
- HTTP layer — *receives* — API request
- PUT — *means* — replace/update this resource
- PATCH — *means* — partially update this resource
- Request thread — *is a* — worker
- Request thread — *handles* — API request
- Hook — *is* — code
- Hook — *runs* — automatically
- CDI observer — *is a* — Java/Jakarta mechanism
- CDI observer — *publishes an* — event
- CDI observer — *reacts to an* — event
- Async observer — *is an* — observer
- Async observer — *reacts* — in the background
- Async observer — *does not block* — publisher
- Allowlist — *is a* — list of tenants
- Allowlist — *enables* — this feature

%% ai-graph-end %%