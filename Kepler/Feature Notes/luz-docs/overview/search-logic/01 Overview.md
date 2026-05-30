---
ai_hash: 281fe8c56993d25b
ai_model: google/gemini-2.5-flash
ai_updated: '2026-05-30'
entities:
- luz-docs
- documents
- Google
- web
- JSON object
- database query
- MongoDB
- translation layer
- notes
- REST API
- JSON
- HTTP
- query DSL
- Elasticsearch
- translator
- aggregation pipelines
- JsonStoreSearchQueryUtil
- query operator
- JsonStoreQuerySearchUtil
- Tenant
- KLARA Documents Search (Confluence)
- search-logic
- 02 Endpoints
- POST /luz_docs/api/{tenant_id}/documents
---

# 01 — Overview

## In one paragraph (plain English)

luz-docs lets you search for documents the same way Google lets you search the web — but for one tenant's documents only. You don't write a database query yourself. You send a JSON object that *describes* what you want ("documents from sender 'mobiliar' created in 2024"), and luz-docs translates it into a real database query against MongoDB. The translation layer is what these notes are about.

## What's involved

- A [[Glossary#REST API|REST API]] you call with [[Glossary#JSON|JSON]] over HTTP.
- A query [[Glossary#DSL (Domain-Specific Language)|DSL]] that looks like [[Glossary#Elasticsearch|Elasticsearch]]'s query language.
- A translator that converts your DSL into [[Glossary#MongoDB|MongoDB]] [[Glossary#Aggregation Pipeline|aggregation pipelines]].
- Some automatic filters the server adds on its own (security, deletion status, etc.).

## Where the translation lives

The two Java classes that do the work:

- `JsonStoreSearchQueryUtil` — handles each query operator (`term`, `range`, etc.).
- `JsonStoreQuerySearchUtil` — assembles the full aggregation pipeline.

## REST base path

```
POST /luz_docs/api/{tenant_id}/documents
```

`{tenant_id}` identifies which [[Glossary#Tenant|tenant]]'s data you're searching. Without it the request is rejected.

## Reference

External: [KLARA Documents Search (Confluence)](https://axonivy.atlassian.net/wiki/spaces/LUZ/pages/20525677912/KLARA+Documents+Search)

---

**Navigation:** [[search-logic|↑ index]] · [[02 Endpoints|next →]]

%% ai-graph-start %%

**Related notes:**
- [[search-logic]]
- [[Glossary]]
- [[index]]
- [[08 Facets]]
- [[02 Endpoints]]

**Relations:**
- luz-docs — *lets search for* — documents
- Google — *lets search for* — web
- luz-docs — *uses* — JSON object
- luz-docs — *translates* — JSON object
- JSON object — *translated into* — database query
- database query — *against* — MongoDB
- translation layer — *is about* — notes
- REST API — *uses* — JSON
- JSON — *over* — HTTP
- query DSL — *looks like* — Elasticsearch
- translator — *converts* — query DSL
- query DSL — *converted into* — aggregation pipelines
- aggregation pipelines — *for* — MongoDB
- JsonStoreSearchQueryUtil — *handles* — query operator
- JsonStoreQuerySearchUtil — *assembles* — aggregation pipelines
- POST /luz_docs/api/{tenant_id}/documents — *is* — REST base path
- {tenant_id} — *identifies* — Tenant
- KLARA Documents Search (Confluence) — *is* — reference
- search-logic — *is* — navigation target
- 02 Endpoints — *is* — navigation target

%% ai-graph-end %%