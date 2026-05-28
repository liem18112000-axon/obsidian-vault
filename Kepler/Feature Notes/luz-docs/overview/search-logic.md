# luz-docs Search Logic

> **Reading order** below. Each section is its own note in [[search-logic/]] so you can jump around without scrolling a 600-line file. Every jargon term links to a plain-English definition in [[search-logic/Glossary|Glossary]].

Reference: [KLARA Documents Search (Confluence)](https://axonivy.atlassian.net/wiki/spaces/LUZ/pages/20525677912/KLARA+Documents+Search)

## Map of Contents

1. [[search-logic/01 Overview|01 — Overview]]
   *What luz-docs search is and where the translation layer lives.*

2. [[search-logic/02 Endpoints|02 — Endpoints]]
   *`/search` vs `/count`, their query params, and when to use which.*

3. [[search-logic/03 Request Body|03 — Request Body]]
   *The JSON skeleton: `from`, `size`, `query`, `facets`, `sort`, `includes`, `excludes`.*

4. [[search-logic/04 Query Operators|04 — Query Operators]]
   *`term`, `terms`, `exists`, `not`, `regexp`, `range`, `and`, `or` — each with client syntax, MongoDB output, and an example.*

5. [[search-logic/05 Operator Nesting|05 — Operator Nesting]]
   *How to build complex queries by combining operators. The fulltext-search pattern.*

6. [[search-logic/06 Server Filters|06 — Automatic Server Filters]]
   *Deletion status, being-created guard, security class, credential mapping. Why your result count may be smaller than expected.*

7. [[search-logic/07 Aggregation Pipeline|07 — Aggregation Pipeline Order]]
   *The 9-stage `/search` pipeline and the 12-stage `/count` pipeline, with rationale for the order.*

8. [[search-logic/08 Facets|08 — Facet Aggregations]]
   *Group-by counts, multi-field grouping, multi-facet operators, `missing` facet.*

9. [[search-logic/09 Examples|09 — Common Use-Case Examples]]
   *Copy-pasteable recipes for the queries you'll write most often.*

🗂 [[search-logic/Glossary|Glossary]] — every jargon term in plain English.

---

## At-a-glance

- **What it is:** a JSON query [[search-logic/Glossary#DSL (Domain-Specific Language)|DSL]] (Elasticsearch-shaped) that compiles to [[search-logic/Glossary#MongoDB|MongoDB]] [[search-logic/Glossary#Aggregation Pipeline|aggregation pipelines]].
- **Where the magic happens:** `JsonStoreSearchQueryUtil` (per-operator translation) and `JsonStoreQuerySearchUtil` (pipeline assembly).
- **REST base:** `POST /luz_docs/api/{tenant_id}/documents`
- **Two endpoints:** `/search` (with optional facets) and `/count` (count only, no facets).
- **Safety net:** four automatic filters (deletion, being-created, security class, credentials) are always appended to your query. Two are bypassable; two aren't.
