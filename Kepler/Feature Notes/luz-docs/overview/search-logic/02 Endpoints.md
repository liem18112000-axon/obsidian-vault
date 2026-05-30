---
ai_hash: bd29b2cadbc66744
ai_model: google/gemini-2.5-flash
ai_updated: '2026-05-30'
entities:
- POST /{tenant_id}/documents/search
- documents
- facet aggregations
- DocumentResource.java:370
- include-deleted-records
- boolean
- 'false'
- soft-deleted documents
- Glossary#Soft delete
- skip-security-classes
- security-class check
- security-class
- Glossary#Security class
- include-folder-name
- _folderNames array
- folder lookup
- exclude-total-count
- totalRecordCount
- infinite scroll UIs
- results
- facets
- Glossary#Facet
- POST /{tenant_id}/documents/count
- DocumentResource.java:402
- HTTP 400
- HTTP status codes
- Glossary#HTTP 400 / HTTP status codes
- List documents
- pagination
- dashboard widget
- Facet sidebar
- invoices
- receipts
- 01 Overview
- search-logic
- 03 Request Body
- 02 Endpoints
---

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

%% ai-graph-start %%

**Related notes:**
- [[09 Examples]]
- [[03 Request Body]]
- [[search-logic]]
- [[07 Aggregation Pipeline]]
- [[Glossary]]

**Relations:**
- POST /{tenant_id}/documents/search — *returns* — documents
- POST /{tenant_id}/documents/search — *supports* — facet aggregations
- POST /{tenant_id}/documents/search — *is_defined_in* — DocumentResource.java:370
- POST /{tenant_id}/documents/search — *has_parameter* — include-deleted-records
- include-deleted-records — *is_type* — boolean
- include-deleted-records — *has_default* — false
- include-deleted-records — *includes* — soft-deleted documents
- soft-deleted documents — *is_a_concept_in* — Glossary#Soft delete
- POST /{tenant_id}/documents/search — *has_parameter* — skip-security-classes
- skip-security-classes — *is_type* — boolean
- skip-security-classes — *has_default* — false
- skip-security-classes — *bypasses* — security-class check
- security-class check — *relates_to* — security-class
- security-class — *is_a_concept_in* — Glossary#Security class
- POST /{tenant_id}/documents/search — *has_parameter* — include-folder-name
- include-folder-name — *is_type* — boolean
- include-folder-name — *has_default* — false
- include-folder-name — *adds* — _folderNames array
- include-folder-name — *avoids* — folder lookup
- POST /{tenant_id}/documents/search — *has_parameter* — exclude-total-count
- exclude-total-count — *is_type* — boolean
- exclude-total-count — *has_default* — false
- exclude-total-count — *skips_computation_of* — totalRecordCount
- exclude-total-count — *improves_performance_for* — infinite scroll UIs
- POST /{tenant_id}/documents/search — *response_includes* — totalRecordCount
- POST /{tenant_id}/documents/search — *response_includes* — results
- POST /{tenant_id}/documents/search — *response_includes* — facets
- totalRecordCount — *represents* — total matched documents
- results — *represents* — current page documents
- facets — *is_present_conditionally* — request asked for facets
- facet aggregations — *is_a_concept_in* — Glossary#Facet
- POST /{tenant_id}/documents/count — *counts* — documents
- POST /{tenant_id}/documents/count — *is_defined_in* — DocumentResource.java:402
- POST /{tenant_id}/documents/count — *does_not_return* — results
- POST /{tenant_id}/documents/count — *does_not_return* — facets
- POST /{tenant_id}/documents/count — *passing_parameter* — facets
- passing_parameter — *causes* — HTTP 400
- HTTP 400 — *is_a* — HTTP status codes
- HTTP 400 — *is_a_concept_in* — Glossary#HTTP 400 / HTTP status codes
- HTTP status codes — *is_a_concept_in* — Glossary#HTTP 400 / HTTP status codes
- POST /{tenant_id}/documents/count — *shares_parameters_with* — POST /{tenant_id}/documents/search
- POST /{tenant_id}/documents/count — *does_not_support_parameter* — include-folder-name
- POST /{tenant_id}/documents/count — *does_not_support_parameter* — exclude-total-count
- POST /{tenant_id}/documents/count — *response_includes* — totalRecordCount
- POST /{tenant_id}/documents/search — *is_used_for* — List documents
- POST /{tenant_id}/documents/search — *is_used_for* — pagination
- POST /{tenant_id}/documents/count — *is_used_for* — dashboard widget
- POST /{tenant_id}/documents/search — *is_used_for* — Facet sidebar
- Facet sidebar — *can_display* — invoices
- Facet sidebar — *can_display* — receipts
- 02 Endpoints — *precedes* — 03 Request Body
- 02 Endpoints — *follows* — 01 Overview
- 02 Endpoints — *is_part_of* — search-logic

%% ai-graph-end %%