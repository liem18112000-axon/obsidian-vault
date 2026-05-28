---
type: technical-section
topic: luz-docs search logic
level: beginner
---

# 06 Facets

Parent: [[Kepler/Feature Notes/luz-docs/overview/search-logic.md]]

Related:
- [[02 Endpoints and Request Body]]
- [[08 Beginner Technical Terms]]

---

## What is a facet?

A **facet** is a grouped count shown alongside search results.

Shopping website analogy:

When you search for laptops, the left sidebar may show:

```text
Brand
- Dell (120)
- Lenovo (80)
- Apple (50)

Price
- 0-500 (30)
- 500-1000 (100)
```

Those grouped counts are facets.

In `luz-docs`, facets are requested using the `facets` key.

---

## Basic terms facet

Request:

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

Beginner meaning:

> Group matching documents by `senderName` and count how many documents each sender has.

Response:

```json
{
  "facets": {
    "My Companies": {
      "terms": [
        { "key": "mobiliar", "count": 10 },
        { "key": "ubs", "count": 2 }
      ]
    }
  }
}
```

---

## Facet pagination

Facet options:

```json
{
  "from": 0,
  "size": 100
}
```

Meaning:

> Return the first 100 facet buckets.

A **bucket** is one group, such as:

```json
{ "key": "mobiliar", "count": 10 }
```

---

## Multi-field grouping

You can group by multiple fields using `fields`.

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

Beginner meaning:

> Group by the combination of sender tenant and sender company.

Example groups:

```text
(tenantA, company1)
(tenantA, company2)
(tenantB, company1)
```

---

## Multi-facet operators

Defined in:

```text
MultiFacetOperator.java
```

| Operator | Beginner explanation |
|---|---|
| `getMax` | Return the maximum value in each group. |
| `getMin` | Return the minimum value in each group. |
| `getFirst` | Return the first value encountered in each group. |
| `getLast` | Return the last value encountered in each group. |
| `addToSet` | Collect distinct values into an array. |
| `countByValue` | Count occurrences per distinct value. |
| `isExisting` | Tell whether a field exists. |

Example: group and get max date:

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

Meaning:

> For each sender tenant/company pair, also return the newest document reference date.

---

## Missing facet

Request:

```json
{
  "facets": {
    "With unknown sender": {
      "missing": {
        "field": "senderTenantIds"
      }
    }
  }
}
```

Meaning:

> Count documents where `senderTenantIds` is absent, null, or empty.

This is useful for UI filters like:

```text
Unknown sender (15)
```

