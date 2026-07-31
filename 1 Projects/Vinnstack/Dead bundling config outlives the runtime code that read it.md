---
ai_hash: 54c869777b706cc9
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-07
entities:
- Dead bundling config
- runtime code
- bundled resource
- packaging/bundling config
- vinnstack commit
- electron/main.js
- resolveCloudSqlProxyPath()
- cloud-sql-proxy
- PATH
- gcloud components install cloud-sql-proxy
- process.resourcesPath
- repo-root
- package.json
- electron-builder
- build.extraResources block
- cloud-sql-proxy.exe
- packaged app
- CI
- extraResources entry
- cloudbuild.yaml
- webpack
- Docker COPY
- phantom build-time requirement
- Vinnstack bundles cloud-sql-proxy.exe as a gitignored extraResource
- Re-check live dependencies right before committing in a shared repo
- Vinnstack publishes its exe to a GCS bucket, not Artifact Registry
source: vinnstack cloudbuild.yaml setup, 2026-07-07
status: seedling
tags:
- vinnstack
- electron-builder
- dead-code
- packaging
title: Dead bundling config outlives the runtime code that read it
type: lesson
---

# Dead bundling config outlives the runtime code that read it

When a runtime behavior change removes the last code path that reads a bundled resource, the packaging/bundling config for that resource can easily survive as dead weight — nobody deletes it because deleting runtime code and deleting build config are two different, easy-to-forget steps.

Concretely: a vinnstack commit changed `electron/main.js`'\''s `resolveCloudSqlProxyPath()` to resolve `cloud-sql-proxy` purely from PATH (installed once via `gcloud components install cloud-sql-proxy`), removing every check of `process.resourcesPath`/repo-root. But `package.json`'\''s electron-builder `build.extraResources` block was left bundling `cloud-sql-proxy.exe` into the packaged app anyway — dead config with no reader, that still forced anyone packaging the app (including CI) to keep supplying that file for zero runtime benefit. Fix: delete the `extraResources` entry from `package.json`, which then let a whole CI step (downloading `cloud-sql-proxy.exe` before packaging) be deleted from `cloudbuild.yaml` as unnecessary too — the dead config was blocking a simplification one level up as well.

General lesson: whenever you remove the last reader of a bundled/packaged resource, grep for that resource'\''s filename in packaging config (electron-builder, webpack, Docker COPY, etc.) and delete it too — otherwise it persists as a phantom build-time requirement.

## Related
[[Vinnstack bundles cloud-sql-proxy.exe as a gitignored extraResource]]
[[Re-check live dependencies right before committing in a shared repo]]
[[Vinnstack publishes its exe to a GCS bucket, not Artifact Registry]]

%% ai-graph-start %%

**Related notes:**
- [[Vinnstack bundles cloud-sql-proxy.exe as a gitignored extraResource]]
- [[Re-check live dependencies right before committing in a shared repo]]
- [[Testing the packaged Vinnstack exe needs databaseUrl in config.json, pins port 3001, portable stub doesn't inherit ad-hoc env]]
- [[Open-and-degrade beats hard-quit let a desktop app start without its optional DB (Vinnstack clean-Win fix)]]
- [[gcloud components install fetches the host's own platform binary, not a chosen target]]

**Relations:**
- Dead bundling config — *outlives* — runtime code
- runtime code — *reads* — bundled resource
- packaging/bundling config — *is for* — bundled resource
- packaging/bundling config — *can survive as* — dead weight
- vinnstack commit — *changed* — electron/main.js
- electron/main.js — *contains function* — resolveCloudSqlProxyPath()
- resolveCloudSqlProxyPath() — *resolves* — cloud-sql-proxy
- cloud-sql-proxy — *from* — PATH
- cloud-sql-proxy — *installed via* — gcloud components install cloud-sql-proxy
- resolveCloudSqlProxyPath() — *removed check of* — process.resourcesPath
- resolveCloudSqlProxyPath() — *removed check of* — repo-root
- package.json — *uses* — electron-builder
- electron-builder — *configures* — build.extraResources block
- build.extraResources block — *bundles* — cloud-sql-proxy.exe
- cloud-sql-proxy.exe — *into* — packaged app
- build.extraResources block — *is* — dead config
- build.extraResources block — *has no* — reader
- build.extraResources block — *forced* — CI
- CI — *to supply* — cloud-sql-proxy.exe
- Fix — *is to delete* — extraResources entry
- extraResources entry — *is in* — package.json
- deletion of extraResources entry — *enabled deletion from* — cloudbuild.yaml
- cloudbuild.yaml — *contains* — CI step
- CI step — *downloads* — cloud-sql-proxy.exe
- dead config — *blocked* — simplification
- packaging config — *includes* — electron-builder
- packaging config — *includes* — webpack
- packaging config — *includes* — Docker COPY
- packaging config — *persists as* — phantom build-time requirement
- Dead bundling config outlives the runtime code that read it — *related to* — Vinnstack bundles cloud-sql-proxy.exe as a gitignored extraResource
- Dead bundling config outlives the runtime code that read it — *related to* — Re-check live dependencies right before committing in a shared repo
- Dead bundling config outlives the runtime code that read it — *related to* — Vinnstack publishes its exe to a GCS bucket, not Artifact Registry

%% ai-graph-end %%