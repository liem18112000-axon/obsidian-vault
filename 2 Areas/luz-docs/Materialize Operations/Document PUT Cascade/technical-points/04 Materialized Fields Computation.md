---
ai_hash: 0a05bd7e339a97be
ai_model: google/gemini-2.5-flash
ai_updated: '2026-05-30'
entities:
- Materialized Fields Computation
- document-put-cascade
- Cascade Decision Gate
- Save and Side Effects
- materializeFacade
- MaterializeCascadeService
- onDocumentChange
- repository
- loadFoldersById
- JsonObjectUtil
- FOLDER_IDS
- _folderNames
- MaterializeCompute
- compute
- MaterializeState
- _effectiveSecurityClassCodes
- _isPublic
- folderId
- securityClassCode
- inheritedSecurityClassCode
- doc-1
- f1
- f2
- Invoices
- Contracts
- FINANCE_5
- HR_4
- LEGAL_6
- Computed field
- Union
- Security class code
- Inherited security code
- Public document
- Map
- token
- tenantId
- metadata
- folder documents
- document
---

# Materialized Fields Computation

[[document-put-cascade|Back to index]] | Previous: [[03 Cascade Decision Gate|Cascade decision gate]] | Next: [[05 Save and Side Effects|save and side effects]]

If the decision gate says "yes", the service recomputes materialized fields with:

```java
materializeFacade.onDocumentChange(token, tenantId, metadata)
```

That calls:

```java
MaterializeCascadeService.onDocumentChange(...)
```

## Visual explanation

![[diagrams/04-computation.png]]

## What data is loaded

`onDocumentChange` loads folder documents for the updated `folderIds`:

```java
repository.loadFoldersById(tenantId, token, JsonObjectUtil.getJsonArrayOrEmpty(document, FOLDER_IDS))
```

The repository returns a map in the same order as `folderIds`, so `_folderNames` can line up with `folderIds`.

## What fields are computed

`MaterializeCompute.compute(...)` returns a `MaterializeState` with:

| Materialized field | How it is computed |
|---|---|
| `_folderNames` | For each `folderId`, store the matching folder's name. If the folder is missing, store an empty string for that position. |
| `_effectiveSecurityClassCodes` | Start with the document's own `securityClassCode`, then add each folder's own and inherited security codes. |
| `_isPublic` | `true` only when the document has no own security codes and either there is at least one public folder or the document has no folders. |

## Example

Input metadata:

```json
{
  "_id": "doc-1",
  "folderIds": ["f1", "f2"],
  "securityClassCode": ["FINANCE_5"]
}
```

Loaded folders:

```json
[
  {
    "_id": "f1",
    "name": "Invoices",
    "securityClassCode": ["HR_4"],
    "inheritedSecurityClassCode": []
  },
  {
    "_id": "f2",
    "name": "Contracts",
    "securityClassCode": [],
    "inheritedSecurityClassCode": ["LEGAL_6"]
  }
]
```

Computed technical fields:

```json
{
  "_folderNames": ["Invoices", "Contracts"],
  "_effectiveSecurityClassCodes": ["FINANCE_5", "HR_4", "LEGAL_6"],
  "_isPublic": false
}
```

## Technical terms

| Term | Newbie explanation |
|---|---|
| Computed field | A field calculated from other data. |
| Union | Combining values from multiple lists while avoiding duplicates. |
| Security class code | A label used to control who can access a document or folder. |
| Inherited security code | A security code a folder receives from its parent or hierarchy. |
| Public document | A document with no restricting security codes according to this materialized calculation. |
| Map | A data structure that stores values by key, like folder ID -> folder object. |

%% ai-graph-start %%

**Related notes:**
- [[03 Cascade Decision Gate]]
- [[08 Glossary for Newbies]]
- [[document-put-cascade]]
- [[01 Overview - Folder Rename Cascade]]
- [[03 Cascade Attempt]]

**Relations:**
- Materialized Fields Computation — *is part of* — document-put-cascade
- Materialized Fields Computation — *follows* — Cascade Decision Gate
- Materialized Fields Computation — *precedes* — Save and Side Effects
- materializeFacade — *calls* — onDocumentChange
- onDocumentChange — *is implemented by* — MaterializeCascadeService
- onDocumentChange — *loads* — folder documents
- repository — *executes* — loadFoldersById
- loadFoldersById — *loads* — folder documents
- loadFoldersById — *uses* — tenantId
- loadFoldersById — *uses* — token
- loadFoldersById — *uses* — FOLDER_IDS
- FOLDER_IDS — *is extracted by* — JsonObjectUtil
- repository — *returns* — Map
- Map — *stores* — folder documents
- _folderNames — *aligns with* — folderId
- MaterializeCompute — *executes* — compute
- compute — *returns* — MaterializeState
- MaterializeState — *contains* — _folderNames
- MaterializeState — *contains* — _effectiveSecurityClassCodes
- MaterializeState — *contains* — _isPublic
- _folderNames — *is computed from* — folderId
- _folderNames — *is computed from* — folder documents
- _effectiveSecurityClassCodes — *is computed from* — securityClassCode
- _effectiveSecurityClassCodes — *is computed from* — inheritedSecurityClassCode
- _isPublic — *is computed from* — securityClassCode
- _isPublic — *is computed from* — folderId
- Computed field — *is defined as* — A field calculated from other data
- Union — *is defined as* — Combining values from multiple lists while avoiding duplicates
- Security class code — *is defined as* — A label used to control who can access a document or folder
- Inherited security code — *is defined as* — A security code a folder receives from its parent or hierarchy
- Public document — *is defined as* — A document with no restricting security codes according to this materialized calculation
- Map — *is defined as* — A data structure that stores values by key, like folder ID -> folder object
- doc-1 — *has* — folderId
- doc-1 — *has* — securityClassCode
- f1 — *has* — name
- f1 — *has* — securityClassCode
- f1 — *has* — inheritedSecurityClassCode
- f2 — *has* — name
- f2 — *has* — securityClassCode
- f2 — *has* — inheritedSecurityClassCode
- onDocumentChange — *takes* — token
- onDocumentChange — *takes* — tenantId
- onDocumentChange — *takes* — metadata
- metadata — *represents* — document
- document — *has* — _id
- document — *has* — folderIds
- document — *has* — securityClassCode
- folder documents — *have* — _id
- folder documents — *have* — name
- folder documents — *have* — securityClassCode
- folder documents — *have* — inheritedSecurityClassCode
- Materialized Fields Computation — *recomputes* — _folderNames
- Materialized Fields Computation — *recomputes* — _effectiveSecurityClassCodes
- Materialized Fields Computation — *recomputes* — _isPublic
- _folderNames — *example value* — ["Invoices", "Contracts"]
- _effectiveSecurityClassCodes — *example value* — ["FINANCE_5", "HR_4", "LEGAL_6"]
- _isPublic — *example value* — false
- _folderNames — *is a* — Computed field
- _effectiveSecurityClassCodes — *is a* — Computed field
- _isPublic — *is a* — Computed field

%% ai-graph-end %%