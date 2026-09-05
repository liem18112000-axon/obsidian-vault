---
title: "GET import-jobs id showWarning=false returns LegacyImportJob to keep luz_mylife_web working"
created: 2026-08-10
type: lesson
status: seedling
source: "session 2026-08-10 import-jobs usage investigation"
tags: [luz-docs-import, api-versioning, backward-compat, gotcha]
---

# GET import-jobs id showWarning=false returns LegacyImportJob to keep luz_mylife_web working

The `GET {tenant}/import-jobs/{id}` endpoint in `luz_docs_import` (`ImportJobResource.getById`) has a `showWarning` query param defaulting to **false**. On the default path it returns `LegacyImportJob.from(job)` — the **master-compatible shape** — instead of the enriched `ImportJob` (successfulFiles/skippedFiles details, rejectedFiles).

**Why:** the sole consumer, `luz_mylife_web`'s `DashboardController.checkJobStatus()`, polls via `LuzDocsImportContainer.getJob()` which calls `.getAs(ImportJob.class)` **without** sending `showWarning`. So the default branch exists specifically to keep that dashboard deserializing into `ch.klara.luz.mylife.model.ImportJob` unchanged.

**Gotcha / decision:** do NOT change the `showWarning=false` default response shape without updating the mylife consumer's model + wrapper. The enriched shape (`?showWarning=true`) is currently requested by nobody. If mylife ever needs the richer data, `getJob()` must append `?showWarning=true`.

## Related
[[luz_mylife_web is the only consumer of luz_docs_import import-jobs endpoints]]

## Related

- [[luz_mylife_web is the only consumer of luz_docs_import import-jobs endpoints]]
