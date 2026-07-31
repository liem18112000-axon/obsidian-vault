---
ai_hash: ec7fb054c304bee8
ai_model: google/gemini-2.5-flash
ai_updated: '2026-05-30'
entities:
- Folder Rename Cascade
- LUZ-154157
- eArchive backend P1.4.3 cascade changes
- kepler/sprint-157/LUZ-154157-...
- dev
- 3661b7c
- Overview
- Trigger Flow
- Cascade Attempt
- Marker State Machine
- Retry Flow
- Files of Record
- Operational Notes
- Decision Log
- Glossary for Newbies
- folder
- document
- system
- MongoDB
- aggregation-pipeline updateMany
- _folderNames
- folderIds
- PUT /folders/{id}
- PATCH /folders/{id}
- materializeCascade
- tenant
---

# Folder Rename Cascade

> Ticket: **LUZ-154157** - eArchive backend P1.4.3 cascade changes
> Branch: `kepler/sprint-157/LUZ-154157-...`
> Status: rolled out to dev (latest tag `3661b7c`)

## Start here

This note explains what happens when a folder is renamed and the system must update every document that stores that folder name.

If you are new to the topic, read in this order:

1. [[technical-points/01 Overview - Folder Rename Cascade|Overview]]
2. [[technical-points/02 Trigger Flow|Trigger flow]]
3. [[technical-points/03 Cascade Attempt|Cascade attempt]]
4. [[technical-points/04 Marker State Machine|Marker state machine]]
5. [[technical-points/05 Retry Flow|Retry flow]]
6. [[technical-points/06 Files of Record|Files of record]]
7. [[technical-points/07 Operational Notes|Operational notes]]
8. [[technical-points/08 Decision Log|Decision log]]
9. [[technical-points/09 Glossary for Newbies|Glossary for newbies]]

## TL;DR

When a folder is renamed through `PUT /folders/{id}` or `PATCH /folders/{id}`, every document that references that folder must update the matching entry in its materialized `_folderNames` array.

The update runs on the server in one MongoDB aggregation-pipeline `updateMany`, without paging through documents one page at a time. If MongoDB reports that some matched documents were not modified, the system keeps a row in the tenant's `materializeCascade` collection so a later request can retry the unfinished work.

## Obsidian map

```mermaid
flowchart TD
    A[Folder Rename Cascade] --> B[Overview]
    B --> C[Trigger Flow]
    C --> D[Cascade Attempt]
    D --> E[Marker State Machine]
    E --> F[Retry Flow]
    A --> G[Files of Record]
    A --> H[Operational Notes]
    A --> I[Decision Log]
    A --> J[Glossary for Newbies]
```

## Key idea in plain English

Think of a document as storing two matching lists:

```text
folderIds:     [123, 456]
_folderNames:  [Inbox, Contracts]
```

If folder `456` is renamed from `Contracts` to `Legal`, the system only changes the second name:

```text
folderIds:     [123, 456]
_folderNames:  [Inbox, Legal]
```

The folder ID stays stable. Only the stored display name changes.

%% ai-graph-start %%

**Related notes:**
- [[01 Overview - Folder Rename Cascade]]
- [[03 Cascade Attempt]]
- [[07 Operational Notes]]
- [[02 Trigger Flow]]
- [[05 Retry Flow]]

**Relations:**
- Folder Rename Cascade — *is_ticket* — LUZ-154157
- LUZ-154157 — *is_for* — eArchive backend P1.4.3 cascade changes
- LUZ-154157 — *has_branch* — kepler/sprint-157/LUZ-154157-...
- Folder Rename Cascade — *rolled_out_to* — dev
- dev — *has_latest_tag* — 3661b7c
- Folder Rename Cascade — *explains* — folder rename process
- Folder Rename Cascade — *involves* — document
- Folder Rename Cascade — *involves* — folder
- Folder Rename Cascade — *has_section* — Overview
- Folder Rename Cascade — *has_section* — Trigger Flow
- Folder Rename Cascade — *has_section* — Cascade Attempt
- Folder Rename Cascade — *has_section* — Marker State Machine
- Folder Rename Cascade — *has_section* — Retry Flow
- Folder Rename Cascade — *has_section* — Files of Record
- Folder Rename Cascade — *has_section* — Operational Notes
- Folder Rename Cascade — *has_section* — Decision Log
- Folder Rename Cascade — *has_section* — Glossary for Newbies
- Overview — *leads_to* — Trigger Flow
- Trigger Flow — *leads_to* — Cascade Attempt
- Cascade Attempt — *leads_to* — Marker State Machine
- Marker State Machine — *leads_to* — Retry Flow
- folder — *renamed_by* — PUT /folders/{id}
- folder — *renamed_by* — PATCH /folders/{id}
- document — *references* — folder
- document — *stores* — _folderNames
- document — *stores* — folderIds
- system — *updates* — _folderNames
- system — *uses* — MongoDB
- system — *uses* — aggregation-pipeline updateMany
- system — *manages* — materializeCascade
- materializeCascade — *stores_unfinished_work_for* — tenant
- _folderNames — *is_updated_by* — system
- folderIds — *is_paired_with* — _folderNames

%% ai-graph-end %%