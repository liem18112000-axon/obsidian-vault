# 02 — Endpoints

Two endpoints. One returns documents, the other just counts them.

## `POST /{tenant_id}/documents/search`

Search documents and optionally compute [[Glossary#Facet|facet]] aggregations.

**Source:** `DocumentResource.java:370`

### Query parameters

| Parameter | Type | Default | What it does (plain English) |
|-----------|------|---------|------------------------------|
| `include-deleted-records` | boolean | `false` | Include [[Glossary#Soft delete\|soft-deleted]] documents in results. By default they're hidden. |
| `skip-security-classes` | boolean | `false` | Bypass the [[Glossary#Security class\|security-class]] check. Admins only — normal users should never set this. |
| `include-folder-name` | boolean | `false` | Add a `_folderNames` array to each result, so you don't need a separate folder lookup. |
| `exclude-total-count` | boolean | `false` | Skip computing `totalRecordCount`. Faster for "infinite scroll" UIs where you don't show a total. |

### Response shape

```json
{
  "totalRecordCount": 42,
  "results": [ { "_id": "...", ... } ],
  "facets": { ... }
}
```

- `totalRecordCount` — how many documents matched in total (across all pages).
- `results` — this page's documents.
- `facets` — only present if you asked for facets in the request.

---

## `POST /{tenant_id}/documents/count`

Just count, no result list, no facets.

**Source:** `DocumentResource.java:402`

> ⚠ Passing `facets` to `/count` returns [[Glossary#HTTP 400 / HTTP status codes|HTTP 400]]. Use `/search` instead.

### Query parameters

Same as `/search`, **except** `include-folder-name` and `exclude-total-count` aren't available (they'd make no sense for a count).

### Response shape

```json
{ "totalRecordCount": 42 }
```

---

## Which to use?

| You want… | Use |
|-----------|-----|
| List documents + maybe paginate | `/search` |
| Just a number for a dashboard widget | `/count` |
| Facet sidebar ("12 invoices, 7 receipts") | `/search` with `facets` |

---

**Navigation:** [[01 Overview|← prev]] · [[search-logic|↑ index]] · [[03 Request Body|next →]]
