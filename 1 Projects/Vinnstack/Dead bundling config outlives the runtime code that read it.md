---
title: "Dead bundling config outlives the runtime code that read it"
created: 2026-07-07
type: lesson
status: seedling
source: "vinnstack cloudbuild.yaml setup, 2026-07-07"
tags: [vinnstack, electron-builder, dead-code, packaging]
---

# Dead bundling config outlives the runtime code that read it

When a runtime behavior change removes the last code path that reads a bundled resource, the packaging/bundling config for that resource can easily survive as dead weight — nobody deletes it because deleting runtime code and deleting build config are two different, easy-to-forget steps.

Concretely: a vinnstack commit changed `electron/main.js`'\''s `resolveCloudSqlProxyPath()` to resolve `cloud-sql-proxy` purely from PATH (installed once via `gcloud components install cloud-sql-proxy`), removing every check of `process.resourcesPath`/repo-root. But `package.json`'\''s electron-builder `build.extraResources` block was left bundling `cloud-sql-proxy.exe` into the packaged app anyway — dead config with no reader, that still forced anyone packaging the app (including CI) to keep supplying that file for zero runtime benefit. Fix: delete the `extraResources` entry from `package.json`, which then let a whole CI step (downloading `cloud-sql-proxy.exe` before packaging) be deleted from `cloudbuild.yaml` as unnecessary too — the dead config was blocking a simplification one level up as well.

General lesson: whenever you remove the last reader of a bundled/packaged resource, grep for that resource'\''s filename in packaging config (electron-builder, webpack, Docker COPY, etc.) and delete it too — otherwise it persists as a phantom build-time requirement.

## Related
[[Vinnstack bundles cloud-sql-proxy.exe as a gitignored extraResource]]
[[Re-check live dependencies right before committing in a shared repo]]
[[Vinnstack publishes its exe to a GCS bucket, not Artifact Registry]]
