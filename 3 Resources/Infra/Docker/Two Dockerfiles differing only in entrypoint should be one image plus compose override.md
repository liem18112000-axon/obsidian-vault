---
ai_hash: fa863b7b6bf06eb7
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-03
entities: []
source: session 2026-07-03
status: seedling
tags:
- docker
- docker-compose
- entrypoint
- image-consolidation
title: Two Dockerfiles differing only in entrypoint should be one image plus compose
  override
type: lesson
---

# Two Dockerfiles differing only in entrypoint should be one image plus compose override

Decision (2026-07-03): merged `Dockerfile.puller` into the single connector `Dockerfile`. The separate puller image existed only because it alone needed boto3; once the main connector grew S3 paths of its own (`APPSFLYER_RAW_STORE=s3` raw landing, `APPSFLYER_CDP_STORE=s3` JSONL store), both images needed `pip install ".[puller]"` and became identical except for the entrypoint. Pattern: when two Dockerfiles differ only in ENTRYPOINT, keep ONE image and expose the second CLI as another docker-compose service with an `entrypoint:` override (both services share `image:` + `build:` so compose builds once). Cuts image-maintenance in half and keeps the two CLIs version-locked.

Related: [[1 Projects/appsflyer-connector/AppsFlyer package layout package-per-concern with no loose modules]]

## Related

- [[1 Projects/appsflyer-connector/AppsFlyer package layout package-per-concern with no loose modules]]

%% ai-graph-start %%

**Related notes:**
- [[AppsFlyer package layout package-per-concern with no loose modules]]
- [[Separate docker-compose files are isolated networks; use one file + a profile for optional services]]
- [[BuildKit honors a per-Dockerfile .dockerignore]]
- [[AppsFlyer connector dropped Kafka sink and S3 raw landing]]
- [[AppsFlyer connector reduced to a single JSONL file-S3 sink]]

%% ai-graph-end %%