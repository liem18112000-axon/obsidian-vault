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

