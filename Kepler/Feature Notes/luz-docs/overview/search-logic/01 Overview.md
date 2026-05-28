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
