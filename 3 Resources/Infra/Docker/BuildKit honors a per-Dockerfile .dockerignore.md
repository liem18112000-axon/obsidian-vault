---
ai_hash: 11934c2c7ec54b88
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-20
entities: []
source: session 2026-06-20 appsflyer-data-connector ITs
status: seedling
tags:
- docker
- buildkit
- dockerignore
- gotcha
title: BuildKit honors a per-Dockerfile .dockerignore
type: lesson
---

# BuildKit honors a per-Dockerfile .dockerignore

BuildKit looks for a dockerignore file named after the specific Dockerfile — `<dockerfile-name>.dockerignore` (e.g. `Dockerfile.it.dockerignore`) — in the same directory, and when present uses it **instead of** the default `.dockerignore` for that build.

**Why it matters:** it lets one repo keep heavy/irrelevant paths out of the production image while a sibling test/debug Dockerfile pulls them in. Example: the main `.dockerignore` excludes `tests/` so the prod image stays lean, but `Dockerfile.it` needs the tests — a `Dockerfile.it.dockerignore` that does NOT exclude `tests/` makes `COPY tests ...` work for the IT image without touching the prod build.

**Gotchas:**
- Requires BuildKit (`DOCKER_BUILDKIT=1`, or Docker 23+ where it's default). The legacy builder ignores per-Dockerfile dockerignore and falls back to `.dockerignore`.
- It fully *replaces* the default — it is not merged. List everything you want excluded.

Discovered building the AppsFlyer connector integration-test image (`Dockerfile.it`).

%% ai-graph-start %%

**Related notes:**
- [[Two Dockerfiles differing only in entrypoint should be one image plus compose override]]
- [[Docker Compose path resolution env_file vs build context vs dockerfile]]
- [[Export build artifacts from a multi-stage Docker build via a scratch stage + buildx --output]]
- [[CI build Docker image on every run, push only on non-PR]]
- [[Relocating docker-compose.yml renames the Compose project and orphans volumes]]

%% ai-graph-end %%