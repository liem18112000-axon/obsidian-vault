---
ai_hash: 9f628dc2a1f0af34
ai_model: google/gemini-2.5-flash
ai_updated: '2026-05-30'
entities:
- search-logic
- luz-docs Search Logic
- KLARA Documents Search
- Confluence
- Overview
- Endpoints
- Request Body
- Query Operators
- Operator Nesting
- Server Filters
- Aggregation Pipeline
- Facets
- Examples
- Glossary
- DSL (Domain-Specific Language)
- Elasticsearch
- MongoDB
- MongoDB Aggregation Pipeline
- JsonStoreSearchQueryUtil
- JsonStoreQuerySearchUtil
- POST /luz_docs/api/{tenant_id}/documents
- /search
- /count
- query params
- from
- size
- query
- facets (request body field)
- sort
- includes
- excludes
- term
- terms
- exists
- not
- regexp
- range
- and
- or
- client syntax
- MongoDB output
- fulltext-search pattern
- Automatic Server Filters
- Deletion status filter
- Being-created guard filter
- Security class filter
- Credential mapping filter
- 9-stage /search pipeline
- 12-stage /count pipeline
- Group-by counts
- Multi-field grouping
- Multi-facet operators
- missing facet
- Copy-pasteable recipes
- translation layer
- JSON skeleton
- complex queries
- pipeline assembly
- per-operator translation
- count only
- four automatic filters
- two bypassable filters
- two non-bypassable filters
- jargon term
---

# luz-docs Search Logic

> **Reading order** below. Each section is its own note in the sibling `search-logic/` folder, so you can jump around without scrolling a 600-line file. Every jargon term links to a plain-English definition in [[search-logic/Glossary|Glossary]].

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

%% ai-graph-start %%

**Related notes:**
- [[01 Overview]]
- [[Glossary]]
- [[index]]
- [[04 Query Operators]]
- [[08 Facets]]

**Relations:**
- luz-docs Search Logic — *is_a* — search-logic
- luz-docs Search Logic — *references* — KLARA Documents Search
- KLARA Documents Search — *is_on* — Confluence
- search-logic — *has_section* — Overview
- search-logic — *has_section* — Endpoints
- search-logic — *has_section* — Request Body
- search-logic — *has_section* — Query Operators
- search-logic — *has_section* — Operator Nesting
- search-logic — *has_section* — Server Filters
- search-logic — *has_section* — Aggregation Pipeline
- search-logic — *has_section* — Facets
- search-logic — *has_section* — Examples
- search-logic — *has_section* — Glossary
- Overview — *describes* — luz-docs Search Logic
- Overview — *describes* — translation layer
- Endpoints — *describes* — /search
- Endpoints — *describes* — /count
- Endpoints — *describes* — query params
- Request Body — *describes* — JSON skeleton
- JSON skeleton — *includes* — from
- JSON skeleton — *includes* — size
- JSON skeleton — *includes* — query
- JSON skeleton — *includes* — facets (request body field)
- JSON skeleton — *includes* — sort
- JSON skeleton — *includes* — includes
- JSON skeleton — *includes* — excludes
- Query Operators — *describes* — term
- Query Operators — *describes* — terms
- Query Operators — *describes* — exists
- Query Operators — *describes* — not
- Query Operators — *describes* — regexp
- Query Operators — *describes* — range
- Query Operators — *describes* — and
- Query Operators — *describes* — or
- Query Operators — *includes* — client syntax
- Query Operators — *includes* — MongoDB output
- Operator Nesting — *describes* — complex queries
- Operator Nesting — *describes* — fulltext-search pattern
- Server Filters — *describes* — Automatic Server Filters
- Automatic Server Filters — *include* — Deletion status filter
- Automatic Server Filters — *include* — Being-created guard filter
- Automatic Server Filters — *include* — Security class filter
- Automatic Server Filters — *include* — Credential mapping filter
- Aggregation Pipeline — *describes* — 9-stage /search pipeline
- Aggregation Pipeline — *describes* — 12-stage /count pipeline
- Facets — *describes* — Group-by counts
- Facets — *describes* — Multi-field grouping
- Facets — *describes* — Multi-facet operators
- Facets — *describes* — missing facet
- Examples — *provides* — Copy-pasteable recipes
- Glossary — *defines* — jargon term
- luz-docs Search Logic — *is_a* — JSON query DSL
- JSON query DSL — *is_shaped_like* — Elasticsearch
- JSON query DSL — *compiles_to* — MongoDB
- JSON query DSL — *compiles_to* — MongoDB Aggregation Pipeline
- JsonStoreSearchQueryUtil — *performs* — per-operator translation
- JsonStoreQuerySearchUtil — *performs* — pipeline assembly
- luz-docs Search Logic — *uses* — POST /luz_docs/api/{tenant_id}/documents
- POST /luz_docs/api/{tenant_id}/documents — *has_endpoint* — /search
- POST /luz_docs/api/{tenant_id}/documents — *has_endpoint* — /count
- /search — *supports* — facets (request body field)
- /count — *is_for* — count only
- /count — *does_not_support* — facets (request body field)
- luz-docs Search Logic — *has_safety_net* — automatic filters
- automatic filters — *are_appended_to* — query
- automatic filters — *has_count* — four automatic filters
- four automatic filters — *include* — Deletion status filter
- four automatic filters — *include* — Being-created guard filter
- four automatic filters — *include* — Security class filter
- four automatic filters — *include* — Credential mapping filter
- four automatic filters — *has_subset* — two bypassable filters
- four automatic filters — *has_subset* — two non-bypassable filters

%% ai-graph-end %%