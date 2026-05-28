---
type: technical-section
topic: luz-docs search logic
level: beginner
---

# 02 Endpoints and Request Body

Parent: [[Kepler/Feature Notes/luz-docs/overview/search-logic.md]]

Related:
- [[01 Big Picture]]
- [[03 Query Operators]]
- [[07 Common Use Cases]]

---

## Endpoint 1 — Search documents

```text
POST /{tenant_id}/documents/search
```

Purpose:

> Return matching documents, and optionally return facet aggregations.

Source in code:

```text
DocumentResource.java:370
```

---

## Search query parameters

| Parameter | Default | Beginner explanation |
|---|---:|---|
| `include-deleted-records` | `false` | Include soft-deleted documents too. Normally deleted documents are hidden. |
| `skip-security-classes` | `false` | Bypass security filtering. This is admin-like behavior and must be used carefully. |
| `include-folder-name` | `false` | Add `_folderNames` to each result. This costs extra work because folders must be joined/loaded. |
| `exclude-total-count` | `false` | Do not compute total count. This can be faster because counting can be expensive. |

Example response:

```json
{
  "totalRecordCount": 42,
  "results": [
    { "_id": "...", "documentTitle": "Invoice" }
  ],
  "facets": {}
}
```

---

## Endpoint 2 — Count documents

```text
POST /{tenant_id}/documents/count
```

Purpose:

> Return only the number of matching documents.

Source in code:

```text
DocumentResource.java:402
```

Important:

> Facets are not supported on `/count`. Passing `facets` returns HTTP 400.

Example response:

```json
{
  "totalRecordCount": 42
}
```

---

## Request body shape

```json
{
  "from": 0,
  "size": 10,
  "query": {},
  "facets": {},
  "sort": { "fieldName": "asc" },
  "includes": ["field1", "field2"],
  "excludes": ["field3"]
}
```

---

## Field-by-field explanation

| Field | Meaning | Default |
|---|---|---|
| `from` | Pagination offset. How many matching records to skip before returning results. | `0` |
| `size` | Number of records to return. | `10` |
| `query` | The search condition. See [[03 Query Operators]]. | empty |
| `facets` | Aggregations/counts grouped by fields. See [[06 Facets]]. | empty |
| `sort` | Sort results by one or more fields. | none |
| `includes` | Only return these fields. | all fields |
| `excludes` | Remove these fields from returned documents. | none |

---

## Pagination: `from` and `size`

Pagination means returning one page of results at a time.

Example:

```json
{
  "from": 20,
  "size": 10
}
```

Beginner meaning:

```text
Skip the first 20 matching documents.
Return the next 10.
```

This is page 3 if page size is 10.

---

## Sorting

Sorting controls result order.

```json
{
  "sort": {
    "documentReferenceDate": "desc"
  }
}
```

Meaning:

> Newest document reference date first.

`asc` means ascending:

```text
A -> Z
old -> new
small -> large
```

`desc` means descending:

```text
Z -> A
new -> old
large -> small
```

---

## Includes and excludes

`includes` is an allowlist:

```json
{
  "includes": ["documentTitle", "senderName"]
}
```

Only those fields are returned.

`excludes` is a denylist:

```json
{
  "excludes": ["documentTextContent", "_files"]
}
```

Everything is returned except those fields.

Why use them?

- smaller response size;
- less network traffic;
- hide large fields like full text or file metadata;
- improve performance.

