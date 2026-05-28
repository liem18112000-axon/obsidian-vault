---
type: technical-section
topic: luz-docs search logic
level: beginner
---

# 01 Big Picture

Parent: [[Kepler/Feature Notes/luz-docs/overview/search-logic.md]]

Related:
- [[02 Endpoints and Request Body]]
- [[03 Query Operators]]
- [[08 Beginner Technical Terms]]

---

## What is this feature?

`luz-docs` exposes a search API for documents.

Plain English:

> A client sends a JSON request saying "find documents like this", and the backend turns that request into MongoDB queries.

The important flow is:

```text
Client JSON request
  -> luz-docs REST endpoint
  -> Elasticsearch-like query DSL
  -> Java translation layer
  -> MongoDB aggregation pipeline
  -> response JSON
```

---

## Why does the API have its own query language?

Instead of forcing every caller to write MongoDB queries directly, the service accepts a controlled JSON query language.

Benefits:

- callers do not need to know MongoDB syntax;
- backend can validate and sanitize queries;
- backend can add security filters automatically;
- backend can change MongoDB implementation details without changing the public API;
- the API can look similar to Elasticsearch-style search requests.

---

## Main Java translation classes

The original note mentions two core utility classes:

```text
JsonStoreSearchQueryUtil
JsonStoreQuerySearchUtil
```

Beginner meaning:

| Class | Beginner explanation |
|---|---|
| `JsonStoreSearchQueryUtil` | Converts client query operators like `term`, `range`, `and`, `or` into MongoDB query pieces. |
| `JsonStoreQuerySearchUtil` | Builds the larger MongoDB aggregation pipeline, including filters, lookup, pagination, projection, and security logic. |

---

## REST base

The REST base is:

```text
POST /luz_docs/api/{tenant_id}/documents
```

`{tenant_id}` is a placeholder.

Example:

```text
POST /luz_docs/api/acme/documents/search
```

Here, `acme` is the tenant id.

---

## Mental model

Imagine a restaurant:

| API concept | Restaurant analogy |
|---|---|
| Client request | Customer order |
| Query DSL | Menu language |
| Translation layer | Waiter translating order for kitchen |
| MongoDB aggregation pipeline | Kitchen cooking steps |
| Response | Finished dish |

The client does not tell the kitchen exactly how to cook. It describes what it wants. The backend translates that into database operations.

---

## What problem does this solve?

The API must support:

- searching by exact fields;
- searching by multiple values;
- checking whether fields exist;
- fulltext-like search using regex;
- date and numeric ranges;
- sorting;
- pagination;
- field include/exclude;
- facets / aggregations;
- security filtering;
- soft-deletion filtering.

That is why the search logic is bigger than a simple `find()` query.

