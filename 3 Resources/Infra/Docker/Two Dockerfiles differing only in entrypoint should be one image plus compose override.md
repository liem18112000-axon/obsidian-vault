---
title: "Two Dockerfiles differing only in entrypoint should be one image plus compose override"
created: 2026-07-03
type: lesson
status: seedling
source: "session 2026-07-03"
tags: [docker, docker-compose, entrypoint, image-consolidation]
---

# Two Dockerfiles differing only in entrypoint should be one image plus compose override

Decision (2026-07-03): merged `Dockerfile.puller` into the single connector `Dockerfile`. The separate puller image existed only because it alone needed boto3; once the main connector grew S3 paths of its own (`APPSFLYER_RAW_STORE=s3` raw landing, `APPSFLYER_CDP_STORE=s3` JSONL store), both images needed `pip install ".[puller]"` and became identical except for the entrypoint. Pattern: when two Dockerfiles differ only in ENTRYPOINT, keep ONE image and expose the second CLI as another docker-compose service with an `entrypoint:` override (both services share `image:` + `build:` so compose builds once). Cuts image-maintenance in half and keeps the two CLIs version-locked.

Related: [[1 Projects/appsflyer-connector/AppsFlyer package layout package-per-concern with no loose modules]]

## Related

- [[1 Projects/appsflyer-connector/AppsFlyer package layout package-per-concern with no loose modules]]
