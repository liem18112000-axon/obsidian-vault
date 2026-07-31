---
ai_hash: 42b5c4c0efad8300
ai_model: google/gemini-2.5-flash
ai_updated: '2026-05-30'
entities:
- Folder Rename Cascade
- Documents
- folder IDs
- folderIds
- folder names
- _folderNames
- Materialized data
- folder
- Inbox
- Contracts
- folder-1
- folder-2
- Legal
- old implementation
- new implementation
- MongoDB
- Cascade
- Materialized field
- Tenant
- Collection
- Document
- Server-side update
- Java app
- Trigger Flow
- reads
- request
- source
- record
- table
- row
- JSON
---

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

%% ai-graph-start %%

**Related notes:**
- [[folder-rename-cascade]]
- [[03 Cascade Attempt]]
- [[03 Cascade Decision Gate]]
- [[02 Trigger Flow]]
- [[09 Glossary for Newbies]]

**Relations:**
- Folder Rename Cascade — *solves problem of* — stale folder names
- Documents — *store* — folder IDs
- Documents — *store* — folder names
- folder IDs — *are stored in field* — `folderIds`
- folder names — *are stored in field* — `_folderNames`
- `_folderNames` — *is a type of* — Materialized data
- Materialized data — *is a* — copied, precomputed value
- Materialized data — *is stored on* — Documents
- Materialized data — *enables faster* — reads
- folder — *is renamed* — 
- copied name — *becomes stale when* — folder is renamed
- Documents — *reference* — folder
- Documents — *are updated when* — folder is renamed
- folder-2 — *is renamed to* — Legal
- old implementation — *recomputed* — folder names document by document
- old implementation — *could block* — request
- new implementation — *uses* — MongoDB
- new implementation — *employs* — Server-side update
- Cascade — *definition* — one change causes related changes
- Cascade — *example* — renaming a folder causes documents to update their copied folder name
- Materialized field — *definition* — value copied onto a record
- Materialized field — *purpose* — make reads easier or faster
- Materialized field — *requires* — syncing when source changes
- Tenant — *definition* — one customer's isolated data area
- Tenant — *has its own* — Collection
- Collection — *is* — MongoDB's version of a table
- Collection — *stores* — Documents
- Document — *is* — MongoDB's version of a row or record
- Document — *is shaped like* — JSON
- Server-side update — *definition* — MongoDB does the work itself
- Server-side update — *contrasts with* — Java app loading and updating documents
- Java app — *loads and updates* — Documents
- Folder Rename Cascade — *has next step* — Trigger Flow

%% ai-graph-end %%