---
title: "JsonParsingException EOF offset -1 means an empty response body was parsed"
created: 2026-06-11
type: lesson
status: seedling
source: "session 2026-06-11"
tags: [java, jax-rs, microprofile, json, gotcha, luz-docs]
---

# JsonParsingException EOF offset -1 means an empty response body was parsed

`javax.json.stream.JsonParsingException: Invalid token=EOF at (line no=1, column no=0, offset=-1)` means `Json.createReader(...)` was handed an **empty string** — almost always an empty HTTP response body parsed without checking status.

MicroProfile REST client methods that return `Response` perform **no error mapping**: a 4xx/5xx/204 comes back as a normal `Response` whose body is empty, so `res.readEntity(String.class)` yields `""` and the JSON parser throws EOF — burying the real upstream failure under a parse symptom.

Seen in luz_docs `MaterializeMigrationExecutor` (lines ~73/80): both jsonstore `find` calls parse the body blind; when luz-jsonstore errored for a tenant, the migration logged the EOF parse exception per document instead of the actual status. Fix pattern: check `res.getStatus()` (and body non-empty) before parsing; log status+body on failure.

## Related

- [[luz-docs migration runs on a cron window via LUZ_DOCS_MIGRATION_PROCESSING_CRON]]
