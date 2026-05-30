---
ai_hash: 31bfb0f7d4fc0215
ai_model: google/gemini-2.5-flash
ai_updated: '2026-05-30'
entities:
- 09 Examples
- POST /{tenant_id}/documents/search
- term
- senderName
- documentReferenceDate
- case-sensitive match
- terms
- case-insensitive match
- 04 Query Operators
- exists
- folderIds
- array
- root folders
- parentFolderIds
- not exists
- deleted documents
- _deletionStatus
- include-deleted-records=true
- automatic deletion filter
- 06 Server Filters
- Fulltext search
- or
- regexp
- documentTitle
- description
- documentTextContent
- Operator Nesting
- and
- Facet
- facets
- terms facet type
- 08 Facets
- specific fields
- includes
- _createdDate
- 03 Request Body
- heavy fields
- excludes
- _files
- binary refs
- file-size range
- range
- _files.reference._sizeInBytes
- gte
- lte
- date range
- ISO-8601 strings
- $dateFromString
- include-folder-name=true
- _folderNames
---

# 09 — Common Use-Case Examples

Copy-pasteable recipes. Each example below is the request body for `POST /{tenant_id}/documents/search` (unless noted).

---

## All documents of a specific sender, newest first

```json
{
  "from": 0, "size": 50,
  "query": { "term": { "senderName": "mobiliar" } },
  "sort": { "documentReferenceDate": "desc" }
}
```

Uses [[04 Query Operators#term — Exact match|term]] for exact case-sensitive match. If you need case-insensitive, use [[04 Query Operators#terms — Match any of multiple values|terms]] with a one-element list.

---

## All documents that live in any folder

```json
{ "query": { "exists": { "field": "folderIds", "type": "array" } } }
```

`type: "array"` because `folderIds` is a list — see [[04 Query Operators#exists — Field has a value|exists for arrays]].

---

## All root folders (folders with no parent)

```json
{ "query": { "not": { "exists": { "field": "parentFolderIds", "type": "array" } } } }
```

`not exists` on the parent list = no parents = root.

---

## All deleted documents

```json
{ "query": { "term": { "_deletionStatus": "true" } } }
```

> ⚠ Must also pass query parameter `include-deleted-records=true` — otherwise the [[06 Server Filters|automatic deletion filter]] hides them before your query runs.

---

## Fulltext search — one term across multiple fields

```json
{
  "query": {
    "or": [
      { "regexp": { "documentTitle":       ".*Post AG.*" } },
      { "regexp": { "description":         ".*Post AG.*" } },
      { "regexp": { "senderName":          ".*Post AG.*" } },
      { "regexp": { "documentTextContent": ".*Post AG.*" } }
    ]
  }
}
```

`or` = matches if "Post AG" appears in *any* of those fields. See [[05 Operator Nesting]] for the mental model.

---

## Fulltext search — multiple terms, each must appear

"Mobiliar AND 2021, each in any of these fields":

```json
{
  "query": {
    "and": [
      { "or": [
          { "regexp": { "documentTitle": ".*Mobiliar.*" } },
          { "regexp": { "senderName":    ".*Mobiliar.*" } }
      ]},
      { "or": [
          { "regexp": { "documentTitle": ".*2021.*" } },
          { "regexp": { "senderName":    ".*2021.*" } }
      ]}
    ]
  }
}
```

Pattern: outer `and` of search terms; each term is an inner `or` over the fields you want to search.

---

## Facet — count by deletion status

```json
{
  "facets": {
    "By Status": {
      "terms": { "field": "_deletionStatus", "from": 0, "size": 10, "sort": { "key": "asc" } }
    }
  }
}
```

See [[08 Facets]].

---

## Return only specific fields (small response)

```json
{
  "query": { "term": { "document-type": "invoice" } },
  "includes": ["documentTitle", "senderName", "_createdDate"]
}
```

Good for list views where you only show 3 columns. See [[03 Request Body]].

---

## Exclude heavy fields

```json
{
  "query": { "term": { "document-type": "invoice" } },
  "excludes": ["documentTextContent", "_files"]
}
```

`documentTextContent` (full OCR text) and `_files` (binary refs) are the biggest fields — exclude them when you don't need them.

---

## Filter by file-size range (1 byte to 50 MiB)

```json
{
  "query": {
    "range": {
      "_files.reference._sizeInBytes": { "gte": 1, "lte": 52428800 }
    }
  }
}
```

`52428800` = `50 * 1024 * 1024` bytes = 50 MiB.

---

## Filter by date range

```json
{
  "query": {
    "range": {
      "_createdDate": {
        "gte": "2021-10-11T09:32:51.099Z",
        "lte": "2021-10-20T09:35:23.513Z"
      }
    }
  }
}
```

ISO-8601 strings; the server parses them via `$dateFromString`. See [[04 Query Operators#range — Numeric or date comparison|range]].

---

## Include folder names on each result

Add the query param:
```
POST /{tenant_id}/documents/search?include-folder-name=true
```

Body:
```json
{ "query": { "term": { "senderName": "mobiliar" } } }
```

Each result will have a `"_folderNames": ["Folder A", ...]` array — no second API call needed.

---

**Navigation:** [[08 Facets|← prev]] · [[search-logic|↑ index]]

%% ai-graph-start %%

**Related notes:**
- [[03 Request Body]]
- [[02 Endpoints]]
- [[04 Query Operators]]
- [[search-logic]]
- [[Glossary]]

**Relations:**
- 09 Examples — *describes* — Common Use-Case Examples
- Common Use-Case Examples — *are* — request body for POST /{tenant_id}/documents/search
- term — *performs* — case-sensitive match
- term — *is described in* — 04 Query Operators
- terms — *performs* — case-insensitive match
- terms — *is described in* — 04 Query Operators
- senderName — *is a* — field
- documentReferenceDate — *is a* — field
- exists — *is described in* — 04 Query Operators
- exists — *is used for* — documents that live in any folder
- folderIds — *is of type* — array
- not exists — *is used for* — root folders
- parentFolderIds — *is a* — field
- deleted documents — *are filtered by* — _deletionStatus
- deleted documents — *require query parameter* — include-deleted-records=true
- automatic deletion filter — *hides* — deleted documents
- automatic deletion filter — *is described in* — 06 Server Filters
- Fulltext search — *uses* — or
- Fulltext search — *uses* — regexp
- Fulltext search — *uses* — and
- documentTitle — *is a* — field
- description — *is a* — field
- documentTextContent — *is a* — field
- Operator Nesting — *is a* — concept
- Facet — *is a* — feature
- facets — *is a JSON key for* — Facet
- terms facet type — *is a* — Facet
- 08 Facets — *describes* — Facet
- specific fields — *are returned by* — includes
- includes — *is a* — JSON key
- includes — *is described in* — 03 Request Body
- _createdDate — *is a* — field
- heavy fields — *are excluded by* — excludes
- excludes — *is a* — JSON key
- documentTextContent — *is a* — heavy fields
- _files — *is a* — heavy fields
- _files — *contains* — binary refs
- file-size range — *uses* — range
- range — *is described in* — 04 Query Operators
- _files.reference._sizeInBytes — *is a* — field
- gte — *is a* — range operator
- lte — *is a* — range operator
- date range — *uses* — range
- ISO-8601 strings — *are format for* — date range
- $dateFromString — *parses* — ISO-8601 strings
- include-folder-name=true — *is a* — query parameter
- include-folder-name=true — *returns* — _folderNames
- _folderNames — *is a* — field

%% ai-graph-end %%