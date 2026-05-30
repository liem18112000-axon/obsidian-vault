---
ai_hash: 653c818a074df59b
ai_model: google/gemini-2.5-flash
ai_updated: '2026-05-30'
entities:
- Facets
- Facet Aggregations
- Online Shop
- luz-docs
- Request Body
- Query
- /count endpoint
- /search endpoint
- Basic terms facet
- Multi-field grouping
- Multi-facet operators
- getMax
- getMin
- getFirst
- getLast
- addToSet
- countByValue
- isExisting
- missing facet
- field parameter
- from parameter
- size parameter
- sort parameter
- key sort option
- count sort option
- fields parameter
- senderName field
- senderTenantId field
- senderCompanyId field
- documentReferenceDate field
- senderTenantIds field
- MultiFacetOperator.java
- Aggregation Pipeline
- Examples
- Glossary
---

# 08 — Facet Aggregations

## What's a facet?

Picture the sidebar on an online shop: *"Brand: Nike (12), Adidas (7)"*. Those counts grouped by brand are [[Glossary#Facet|facets]]. In luz-docs you compute them alongside the search results — same request, no second round-trip.

Facets go in a `facets` key on the request body, next to `query`. Each facet has a name you choose.

> Facets are **not supported on `/count`** — they only work on `/search`.

---

## Basic terms facet

Group documents by the value of one field and count each group.

```json
{
  "facets": {
    "My Companies": {
      "terms": {
        "field": "senderName",
        "from": 0,
        "size": 100,
        "sort": { "key": "asc" }
      }
    }
  }
}
```

Response:
```json
{
  "facets": {
    "My Companies": {
      "terms": [
        { "key": "mobiliar", "count": 10 },
        { "key": "ubs",      "count": 2 }
      ]
    }
  }
}
```

- `field` — which field to group by.
- `from`/`size` — paginate the **facet** results (different from query pagination).
- `sort: { "key": "asc" }` — sort facet rows alphabetically by the group value. Use `{ "count": "desc" }` to put the biggest groups first.

---

## Multi-field grouping

Group by multiple fields at once — use `fields` (array) instead of `field`:

```json
{
  "facets": {
    "test": {
      "terms": {
        "fields": ["senderTenantId", "senderCompanyId"],
        "from": 0,
        "size": 10,
        "sort": { "documentReferenceDate": "asc" }
      }
    }
  }
}
```

Each row is one unique combination of (senderTenantId, senderCompanyId).

---

## Multi-facet operators

When you use `fields`, you can also ask for **extra computed values** per group. Defined in `MultiFacetOperator.java`.

| Operator | What it does |
|----------|--------------|
| `getMax` | Maximum value of the listed fields within the group |
| `getMin` | Minimum value |
| `getFirst` | First value encountered (insertion order) |
| `getLast` | Last value encountered |
| `addToSet` | Distinct values collected into an array |
| `countByValue` | Count occurrences of each distinct value |
| `isExisting` | Whether the field exists at all in the group |

### Example — group + latest date per group

"For each (tenantId, companyId), tell me the most recent `documentReferenceDate`":

```json
{
  "facets": {
    "test": {
      "terms": {
        "fields": ["senderTenantId", "senderCompanyId"],
        "getMax": ["documentReferenceDate"],
        "from": 0,
        "size": 10,
        "sort": { "documentReferenceDate": "asc" }
      }
    }
  }
}
```

---

## `missing` facet

Count documents where a field is **absent**, `null`, or `[]`.

```json
{
  "facets": {
    "With unknown sender": {
      "missing": { "field": "senderTenantIds" }
    }
  }
}
```

Useful for "how many of my documents are missing required data?" dashboards.

---

**Navigation:** [[07 Aggregation Pipeline|← prev]] · [[search-logic|↑ index]] · [[09 Examples|next →]]

%% ai-graph-start %%

**Related notes:**
- [[search-logic]]
- [[Glossary]]
- [[04 Query Operators]]
- [[03 Request Body]]
- [[02 Endpoints]]

**Relations:**
- Facets — *ARE* — Facet Aggregations
- Facets — *EXAMPLE_USE_CASE* — Online Shop
- Facets — *COMPUTED_IN* — luz-docs
- Facets — *LOCATED_IN* — Request Body
- Request Body — *CONTAINS_KEY* — Query
- Facets — *NOT_SUPPORTED_ON* — /count endpoint
- Facets — *SUPPORTED_ON* — /search endpoint
- Basic terms facet — *IS_A* — Facets
- Basic terms facet — *USES* — field parameter
- Basic terms facet — *USES* — from parameter
- Basic terms facet — *USES* — size parameter
- Basic terms facet — *USES* — sort parameter
- sort parameter — *HAS_OPTION* — key sort option
- sort parameter — *HAS_OPTION* — count sort option
- Multi-field grouping — *IS_A* — Facets
- Multi-field grouping — *USES* — fields parameter
- Multi-field grouping — *USES* — from parameter
- Multi-field grouping — *USES* — size parameter
- Multi-field grouping — *USES* — sort parameter
- Multi-facet operators — *USED_WITH* — Multi-field grouping
- Multi-facet operators — *DEFINED_IN* — MultiFacetOperator.java
- getMax — *IS_A* — Multi-facet operators
- getMin — *IS_A* — Multi-facet operators
- getFirst — *IS_A* — Multi-facet operators
- getLast — *IS_A* — Multi-facet operators
- addToSet — *IS_A* — Multi-facet operators
- countByValue — *IS_A* — Multi-facet operators
- isExisting — *IS_A* — Multi-facet operators
- missing facet — *IS_A* — Facets
- missing facet — *USES* — field parameter
- senderName field — *IS_EXAMPLE_FIELD_FOR* — Basic terms facet
- senderTenantId field — *IS_EXAMPLE_FIELD_FOR* — Multi-field grouping
- senderCompanyId field — *IS_EXAMPLE_FIELD_FOR* — Multi-field grouping
- documentReferenceDate field — *IS_EXAMPLE_FIELD_FOR* — Multi-field grouping
- senderTenantIds field — *IS_EXAMPLE_FIELD_FOR* — missing facet
- Facets — *PREVIOUS_TOPIC* — Aggregation Pipeline
- Facets — *NEXT_TOPIC* — Examples
- Facets — *DEFINED_IN* — Glossary

%% ai-graph-end %%