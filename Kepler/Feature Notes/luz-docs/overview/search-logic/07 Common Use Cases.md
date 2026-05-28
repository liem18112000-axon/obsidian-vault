---
type: technical-section
topic: luz-docs search logic
level: beginner
---

# 07 Common Use Cases

Parent: [[Kepler/Feature Notes/luz-docs/overview/search-logic.md]]

Related:
- [[02 Endpoints and Request Body]]
- [[03 Query Operators]]
- [[04 Operator Nesting]]

---

## Get all documents of a specific sender

```json
{
  "from": 0,
  "size": 50,
  "query": {
    "term": {
      "senderName": "mobiliar"
    }
  },
  "sort": {
    "documentReferenceDate": "desc"
  }
}
```

Meaning:

> Return the first 50 documents where sender name is exactly `mobiliar`, newest first.

---

## Get documents in any folder

```json
{
  "query": {
    "exists": {
      "field": "folderIds",
      "type": "array"
    }
  }
}
```

Meaning:

> Return documents where the `folderIds` array has at least one folder id.

---

## Get root folders / no parent

```json
{
  "query": {
    "not": {
      "exists": {
        "field": "parentFolderIds",
        "type": "array"
      }
    }
  }
}
```

Meaning:

> Return items whose `parentFolderIds` array is missing or empty.

---

## Get all deleted documents

```json
{
  "query": {
    "term": {
      "_deletionStatus": "true"
    }
  }
}
```

Important:

```text
include-deleted-records=true
```

is required, otherwise the server's automatic deletion filter hides deleted records.

---

## Fulltext-like search: one term, many fields

```json
{
  "query": {
    "or": [
      { "regexp": { "documentTitle": ".*Post AG.*" } },
      { "regexp": { "description": ".*Post AG.*" } },
      { "regexp": { "senderName": ".*Post AG.*" } },
      { "regexp": { "documentTextContent": ".*Post AG.*" } }
    ]
  }
}
```

Meaning:

> Find documents where `Post AG` appears in title, description, sender name, or document text.

---

## Fulltext-like search: many terms, many fields

```json
{
  "query": {
    "and": [
      {
        "or": [
          { "regexp": { "documentTitle": ".*Mobiliar.*" } },
          { "regexp": { "senderName": ".*Mobiliar.*" } }
        ]
      },
      {
        "or": [
          { "regexp": { "documentTitle": ".*2021.*" } },
          { "regexp": { "senderName": ".*2021.*" } }
        ]
      }
    ]
  }
}
```

Meaning:

> `Mobiliar` must appear in at least one searched field, and `2021` must also appear in at least one searched field.

See [[04 Operator Nesting]].

---

## Count documents by deletion status

```json
{
  "facets": {
    "By Status": {
      "terms": {
        "field": "_deletionStatus",
        "from": 0,
        "size": 10,
        "sort": { "key": "asc" }
      }
    }
  }
}
```

Meaning:

> Group documents by deletion status and count each group.

---

## Return only specific fields

```json
{
  "query": {
    "term": {
      "document-type": "invoice"
    }
  },
  "includes": ["documentTitle", "senderName", "_createdDate"]
}
```

Meaning:

> Find invoices, but only return title, sender name, and created date.

---

## Exclude specific fields

```json
{
  "query": {
    "term": {
      "document-type": "invoice"
    }
  },
  "excludes": ["documentTextContent", "_files"]
}
```

Meaning:

> Find invoices, but do not return large text content or file metadata.

---

## Search by file size range

```json
{
  "query": {
    "range": {
      "_files.reference._sizeInBytes": {
        "gte": 1,
        "lte": 52428800
      }
    }
  }
}
```

Meaning:

> Find documents whose file size is between 1 byte and 50 MB.

---

## Search by date range

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

Meaning:

> Find documents created between these two timestamps.

---

## Include folder names

Endpoint:

```text
POST /{tenant_id}/documents/search?include-folder-name=true
```

Body:

```json
{
  "query": {
    "term": {
      "senderName": "mobiliar"
    }
  }
}
```

Meaning:

> Return matching documents and also include folder names, such as `_folderNames: ["Folder A"]`.

