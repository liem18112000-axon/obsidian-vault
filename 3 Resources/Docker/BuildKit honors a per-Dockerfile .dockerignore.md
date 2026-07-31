---
title: "BuildKit honors a per-Dockerfile .dockerignore"
created: 2026-06-20
type: lesson
status: seedling
source: "session 2026-06-20 appsflyer-data-connector ITs"
tags: [docker, buildkit, dockerignore, gotcha]
---

# BuildKit honors a per-Dockerfile .dockerignore

BuildKit looks for a dockerignore file named after the specific Dockerfile — `<dockerfile-name>.dockerignore` (e.g. `Dockerfile.it.dockerignore`) — in the same directory, and when present uses it **instead of** the default `.dockerignore` for that build.

**Why it matters:** it lets one repo keep heavy/irrelevant paths out of the production image while a sibling test/debug Dockerfile pulls them in. Example: the main `.dockerignore` excludes `tests/` so the prod image stays lean, but `Dockerfile.it` needs the tests — a `Dockerfile.it.dockerignore` that does NOT exclude `tests/` makes `COPY tests ...` work for the IT image without touching the prod build.

**Gotchas:**
- Requires BuildKit (`DOCKER_BUILDKIT=1`, or Docker 23+ where it's default). The legacy builder ignores per-Dockerfile dockerignore and falls back to `.dockerignore`.
- It fully *replaces* the default — it is not merged. List everything you want excluded.

Discovered building the AppsFlyer connector integration-test image (`Dockerfile.it`).
