---
title: "Gate enriched REST response behind a boolean query param with a legacy-shaped DTO"
created: 2026-08-05
type: lesson
status: seedling
source: "session 2026-08-05 luz_docs_import showWarning"
tags: [rest, backward-compat, jax-rs, dto, json-b, luz-docs]
---

# Gate enriched REST response behind a boolean query param with a legacy-shaped DTO

To evolve a REST response without breaking existing clients, add a boolean query param (default false) that gates the enrichment, and return a **separate legacy-shaped DTO** for the default case rather than mutating the enriched model.

**Why a separate DTO, not field-nulling:** if the enrichment *changed a field type* (e.g. `successfulFiles` went from `List<String>` to `List<FileFailedInformation>`), you cannot conditionally serialize the old shape from the new class — nulling a `detail` still yields `{filePath, detail:null}`, never a bare string. A dedicated view class with a `from(enriched)` mapper reproduces the old contract exactly (old field types + omitted new fields).

**Mechanics (JAX-RS):** change the resource method to return `Object` and pick `showWarning ? enriched : LegacyView.from(enriched)`. Keep the service returning the full model; do the view shaping at the resource (presentation concern). JSON-B/Yasson serializes by runtime type and omits nulls by default, so absent fields on the legacy DTO simply do not appear.

**Concrete case (luz_docs_import):** `GET import-jobs/{id}?showWarning=false` returns the master shape (successfulFiles as plain paths, no skippedFilesDetail/rejectedFiles); `?showWarning=true` returns the enriched ImportJob with per-file warning detail (e.g. an ignored `.metadata.json` sidecar). Domain nugget: a *successful* file can still carry a non-error warning detail.

Gotcha: OpenAPI `@APIResponse(schema=@Schema(implementation=Enriched.class))` documents only the rich shape; the default lean shape is undocumented unless you add a second schema.

## Related

- [[Backward-compatible API evolution]]
- [[luz-docs document import]]
