---
title: "CI path-filter must mirror the Docker build context, not the service folder"
created: 2026-08-30
type: lesson
status: seedling
source: "session 2026-08-30 leo-customer360"
tags: [ci-cd, github-actions, docker, paths-filter, gotcha]
---

# CI path-filter must mirror the Docker build context, not the service folder

A path-based CI change-filter (e.g. `dorny/paths-filter`) that decides whether to rebuild an image must watch **every directory the image's build context actually reads from** — not just the folder the Dockerfile lives in.

## Why it bites
An image can COPY files that live outside its own directory. In `leo-customer360`, `postgres/Dockerfile` is built with **build context = repo root** and does `COPY database-init/*.sql /docker-entrypoint-initdb.d/`. The CI filter was `postgres: 'postgres/**'`, so editing `database-init/persona360-schema.sql` matched no filter and the postgres image **silently did not rebuild** — the schema change never reached the published image.

## Fix
List all real inputs in the filter:
```yaml
postgres:
  - 'postgres/**'
  - 'database-init/**'
```

## Principle
CI rebuild triggers should mirror the build-context inputs (the Dockerfile's `COPY`/`ADD` sources), not the service's home directory. A build context that reaches "sideways" into sibling folders is the classic trap — audit `COPY` paths against the filter whenever either changes.

## Related
[[leo-customer360 applies DB schema via two paths that must stay in sync]]

## Related

- [[leo-customer360 applies DB schema via two paths that must stay in sync]]
