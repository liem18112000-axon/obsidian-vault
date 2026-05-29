# Overview - Folder Rename Cascade

[[folder-rename-cascade|Back to index]] | Next: [[02 Trigger Flow|Trigger flow]]

## What problem this solves

Documents store folder IDs in `folderIds` and the matching folder names in `_folderNames`.

That second field is **materialized data**: it is a copied, precomputed value stored on the document so reads can be faster and simpler.

When a folder is renamed, the copied name becomes stale unless every document that references the folder is updated.

## Tiny example

Before rename:

```json
{
  "folderIds": ["folder-1", "folder-2"],
  "_folderNames": ["Inbox", "Contracts"]
}
```

Folder `folder-2` is renamed to `Legal`.

After cascade:

```json
{
  "folderIds": ["folder-1", "folder-2"],
  "_folderNames": ["Inbox", "Legal"]
}
```

## Why the cascade exists

The old implementation paged through matching documents and recomputed names document by document. That could block a request for hours when a folder had tens of thousands of documents.

The new implementation asks MongoDB to update all matching documents in one server-side operation.

## Important beginner terms

| Term | Beginner explanation |
|---|---|
| Cascade | One change causes related changes elsewhere. Here: renaming a folder causes documents to update their copied folder name. |
| Materialized field | A value copied onto a record to make reads easier or faster. It must be kept in sync when the source changes. |
| Tenant | One customer's isolated data area. Each tenant has its own collections. |
| Collection | MongoDB's version of a table. A collection stores many documents. |
| Document | MongoDB's version of a row or record. Usually shaped like JSON. |
| Server-side update | MongoDB does the work itself instead of the Java app loading and updating documents one by one. |

