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

