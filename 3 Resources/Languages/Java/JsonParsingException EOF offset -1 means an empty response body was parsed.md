---
ai_hash: ed8a754f31c683e8
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-11
entities: []
source: session 2026-06-11
status: seedling
tags:
- java
- jax-rs
- microprofile
- json
- gotcha
- luz-docs
title: JsonParsingException EOF offset -1 means an empty response body was parsed
type: lesson
---

# JsonParsingException EOF offset -1 means an empty response body was parsed

`javax.json.stream.JsonParsingException: Invalid token=EOF at (line no=1, column no=0, offset=-1)` means `Json.createReader(...)` was handed an **empty string** — almost always an empty HTTP response body parsed without checking status.

MicroProfile REST client methods that return `Response` perform **no error mapping**: a 4xx/5xx/204 comes back as a normal `Response` whose body is empty, so `res.readEntity(String.class)` yields `""` and the JSON parser throws EOF — burying the real upstream failure under a parse symptom.

Seen in luz_docs `MaterializeMigrationExecutor` (lines ~73/80): both jsonstore `find` calls parse the body blind; when luz-jsonstore errored for a tenant, the migration logged the EOF parse exception per document instead of the actual status. Fix pattern: check `res.getStatus()` (and body non-empty) before parsing; log status+body on failure.

## Related

- [[luz-docs migration runs on a cron window via LUZ_DOCS_MIGRATION_PROCESSING_CRON]]

%% ai-graph-start %%

**Related notes:**
- [[luz-jsonstore intermittently returns 200 with empty body on folder finds]]
- [[luz-jsonstore find returns 200 empty string, not [], on zero matches]]
- [[luz-docs getDocumentById returns empty object not null for missing docs]]
- [[Mockito @InjectMocks by type stale @Mock after @RestClient swap leaves real field null]]
- [[empty-object-not-null sentinel defeats Optional.ofNullable null-guards]]

%% ai-graph-end %%