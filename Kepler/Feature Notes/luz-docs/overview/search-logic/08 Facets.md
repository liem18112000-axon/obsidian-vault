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
