---
ai_hash: b93fc6f3578d854f
ai_model: google/gemini-2.5-flash
ai_updated: '2026-05-30'
entities:
- Cascade Attempt
- index
- Trigger flow
- Marker state machine
- Async Observer
- MongoDB
- MongoDB updateMany
- Tenant's documents collection
- Cascade Sequence Diagram
- Cascade Marker
- MaterializeRepository
- MaterializeRepository.startCascadeMarker
- Tenant ID
- Token
- Folder ID
- Materialize Cascade Collection
- START Status
- Lock
- onCascadeRetry
- MongoDB Filter
- MaterializeQueryBuilder
- MaterializeQueryBuilder.buildFolderRenameFilter
- New Folder Name
- folderIds field
- _folderNames field
- $exists operator
- $expr operator
- $and operator
- $eq operator
- $size operator
- $ne operator
- $arrayElemAt operator
- $indexOfArray operator
- Filter Constraint
- MongoDB Pipeline
- MaterializeQueryBuilder.buildFolderRenamePipeline
- $set stage
- $map operator
- $range operator
- $cond operator
- Folders Collection
- Terminal Branches
- matchedCount
- modifiedCount
- MaterializeRepository.deleteCascadeMarker
- Marker ID
- Branch A (Success)
- Branch B (Partial)
- Branch C (Error)
- DocumentException (SC_MULTI_STATUS)
- MaterializeRepository.markCascadeMarkerPartial
- PARTIAL Status
- SocketTimeoutException
- MaterializeCascadeException
- Idempotent
- HTTP 207 / Multi-Status
- Socket Timeout
- Observer
---

# Cascade Attempt

[[folder-rename-cascade|Back to index]] | Previous: [[02 Trigger Flow|Trigger flow]] | Next: [[04 Marker State Machine|Marker state machine]]

Inside the async observer, the cascade is exactly one MongoDB `updateMany` against the tenant's `documents` collection.

![[diagrams/02-cascade-sequence.png]]

## 1. Marker is written before the cascade

`MaterializeRepository.startCascadeMarker(tenantId, token, folderId)` inserts this row into the tenant's `materializeCascade` collection:

```json
{
  "_id": "<auto>",
  "folderId": "<folderId>",
  "_cascadeAt": "<ISO-8601 instant>",
  "status": "START"
}
```

The `START` row acts like a simple lock. `onCascadeRetry` ignores `START` rows, so two concurrent retries do not pick the same folder.

## 2. The filter

`MaterializeQueryBuilder.buildFolderRenameFilter(folderId, newName)` produces:

```json
{
  "folderIds": "<folderId>",
  "_folderNames": { "$exists": true },
  "$expr": {
    "$and": [
      { "$eq": [ {"$size": "$_folderNames"}, {"$size": "$folderIds"} ] },
      { "$ne": [
        { "$arrayElemAt": ["$_folderNames", {"$indexOfArray": ["$folderIds", "<folderId>"]}] },
        "<newName>"
      ]}
    ]
  }
}
```

The filter chooses only documents that are safe and useful to update:

| Constraint | Why it exists |
|---|---|
| `folderIds: <folderId>` | Only update documents that reference the renamed folder. |
| `_folderNames: { $exists: true }` | Skip documents that do not have materialized names yet. Normal materialization will fill them later. |
| Same array length | `_folderNames` and `folderIds` must line up position by position. If lengths differ, updating by position could corrupt data. |
| Current name is not already `newName` | Skip documents already fixed. This makes reruns safe and helps keep `matchedCount == modifiedCount`. |

## 3. The pipeline

`MaterializeQueryBuilder.buildFolderRenamePipeline(folderId, newName)` produces one `$set` stage:

```json
[
  {
    "$set": {
      "_folderNames": {
        "$map": {
          "input": { "$range": [0, {"$size": "$folderIds"}] },
          "as": "i",
          "in": {
            "$cond": [
              { "$eq": [{"$arrayElemAt": ["$folderIds", "$$i"]}, "<folderId>"] },
              "<newName>",
              { "$arrayElemAt": ["$_folderNames", "$$i"] }
            ]
          }
        }
      }
    }
  }
]
```

In plain English:

1. Look at every position in the `folderIds` list.
2. If the folder ID at that position is the renamed folder, put the new folder name at the same position in `_folderNames`.
3. Otherwise, keep the existing folder name.

This works because the cascade touches only one slot. It does not recompute the entire `_folderNames` array from the `folders` collection.

## 4. Terminal branches

| Branch | Mongo / network outcome | Repo return | Service action |
|---|---|---|---|
| A | `matchedCount == modifiedCount` | `true` | `deleteCascadeMarker(markerId)` removes the row. |
| B | `matchedCount != modifiedCount`, causing `DocumentException(SC_MULTI_STATUS)` | `false` | `markCascadeMarkerPartial(markerId)` keeps the row with `status = PARTIAL`. |
| C | `SocketTimeoutException` or another throwable, wrapped as `MaterializeCascadeException` | throws | Observer logs `aborted`; marker stays `START` and needs manual cleanup. |

Branch A is the normal success path. Branch B is what retry handles. Branch C is intentionally not retried automatically because a hard exception could loop forever.

## Beginner terms

| Term | Beginner explanation |
|---|---|
| `updateMany` | MongoDB command that updates every document matching a filter. |
| Filter | The "which documents should we update?" part of the command. |
| Pipeline | A list of steps MongoDB runs to transform documents. |
| `$set` | MongoDB operation that changes or creates a field. |
| `$map` | MongoDB operation that builds a new array by transforming each item. |
| `$cond` | MongoDB's "if this, then that, else something else." |
| `$expr` | Lets a MongoDB filter compare values inside the same document. |
| `matchedCount` | How many documents matched the filter. |
| `modifiedCount` | How many matched documents MongoDB actually changed. |
| Idempotent | Safe to run more than once; repeated runs do not keep changing data incorrectly. |
| HTTP 207 / Multi-Status | A response meaning "the overall operation had mixed results." Here it means not every matched document was modified. |
| Socket timeout | The app waited for a network/database response too long and gave up. |

%% ai-graph-start %%

**Related notes:**
- [[folder-rename-cascade]]
- [[01 Overview - Folder Rename Cascade]]
- [[05 Retry Flow]]
- [[04 Marker State Machine]]
- [[02 Trigger Flow]]

**Relations:**
- Cascade Attempt — *is_described_in* — 03 Cascade Attempt
- Cascade Attempt — *is_preceded_by* — Trigger flow
- Cascade Attempt — *is_followed_by* — Marker state machine
- Cascade Attempt — *returns_to* — index
- Async Observer — *executes* — MongoDB updateMany
- MongoDB updateMany — *targets* — Tenant's documents collection
- Cascade Attempt — *is_illustrated_by* — Cascade Sequence Diagram
- Cascade Marker — *is_written_before* — Cascade Attempt
- MaterializeRepository.startCascadeMarker — *inserts* — Cascade Marker
- Cascade Marker — *is_inserted_into* — Materialize Cascade Collection
- Cascade Marker — *has_field* — _id
- Cascade Marker — *has_field* — Folder ID
- Cascade Marker — *has_field* — _cascadeAt
- Cascade Marker — *has_field* — status
- status — *can_be* — START Status
- START Status — *acts_as* — Lock
- onCascadeRetry — *ignores* — START Status
- MaterializeQueryBuilder.buildFolderRenameFilter — *produces* — MongoDB Filter
- MongoDB Filter — *uses* — Folder ID
- MongoDB Filter — *uses* — New Folder Name
- MongoDB Filter — *includes* — folderIds field
- MongoDB Filter — *includes* — _folderNames field
- MongoDB Filter — *includes* — $exists operator
- MongoDB Filter — *includes* — $expr operator
- MongoDB Filter — *includes* — $and operator
- MongoDB Filter — *includes* — $eq operator
- MongoDB Filter — *includes* — $size operator
- MongoDB Filter — *includes* — $ne operator
- MongoDB Filter — *includes* — $arrayElemAt operator
- MongoDB Filter — *includes* — $indexOfArray operator
- Filter Constraint — *explains* — MongoDB Filter
- MaterializeQueryBuilder.buildFolderRenamePipeline — *produces* — MongoDB Pipeline
- MongoDB Pipeline — *uses* — Folder ID
- MongoDB Pipeline — *uses* — New Folder Name
- MongoDB Pipeline — *contains* — $set stage
- $set stage — *uses* — $map operator
- $set stage — *uses* — $range operator
- $set stage — *uses* — $cond operator
- MongoDB Pipeline — *does_not_recompute_from* — Folders Collection
- Terminal Branches — *describe_outcomes_of* — Cascade Attempt
- Branch A (Success) — *occurs_when* — matchedCount == modifiedCount
- Branch A (Success) — *leads_to* — MaterializeRepository.deleteCascadeMarker
- Branch B (Partial) — *occurs_when* — matchedCount != modifiedCount
- Branch B (Partial) — *causes* — DocumentException (SC_MULTI_STATUS)
- Branch B (Partial) — *leads_to* — MaterializeRepository.markCascadeMarkerPartial
- MaterializeRepository.markCascadeMarkerPartial — *sets_status_to* — PARTIAL Status
- Branch C (Error) — *involves* — SocketTimeoutException
- Branch C (Error) — *involves* — MaterializeCascadeException
- Observer — *logs* — aborted
- Branch C (Error) — *leaves_marker_status_as* — START Status
- MongoDB updateMany — *reports* — matchedCount
- MongoDB updateMany — *reports* — modifiedCount
- Cascade Attempt — *is* — Idempotent
- HTTP 207 / Multi-Status — *is_related_to* — DocumentException (SC_MULTI_STATUS)
- Socket Timeout — *is_related_to* — SocketTimeoutException

%% ai-graph-end %%