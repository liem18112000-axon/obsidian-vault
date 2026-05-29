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

