---
ai_hash: 397485dfc9ced6d8
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-02
entities: []
source: session 2026-07-02; appsflyer unify-compose
status: seedling
tags:
- docker-compose
- profiles
- networking
- minio
- gotcha
title: Separate docker-compose files are isolated networks; use one file + a profile
  for optional services
type: lesson
---

# Separate docker-compose files are isolated networks; use one file + a profile for optional services

Two separate `docker-compose.yml` files (run as `docker compose -f a.yml` and `-f b.yml`) are **independent projects on different default networks**, so a container in one CANNOT reach a service in the other by service name. Symptom in the appsflyer connector: the `connector` (in docker-compose.yml) could not hit MinIO (in docker-compose.minio.yml) via `minio:9000`; inside the connector container `localhost:9000` is the container itself, not MinIO (published host ports only help a process on the host).

**Fix / pattern:** put both services in ONE compose file on a shared network, and gate the OPTIONAL one with a **compose profile** so the default path stays lightweight:

- `minio` + `minio-init` get `profiles: ["s3"]` → NOT started unless the profile is active.
- `connector` has NO profile (always available) and joins the same network.

Gotchas learned:
- `docker compose config --services` lists only default (no-profile) services; `docker compose --profile s3 config --services` includes the profiled ones — a quick way to verify gating.
- Do NOT add `depends_on` from the always-on service to a profiled service: Compose then pulls the profiled dependency into EVERY run, defeating the opt-in. Instead bring the profiled service up separately (`docker compose --profile s3 up -d minio minio-init`), then `docker compose run --rm connector ...`.
- `up -d minio` alone won't start `minio-init` (minio doesn't depend on it) — name both, or the bucket never gets created.
- A service's `environment:` overrides its `env_file:`, so hardcoding `MINIO_ENDPOINT: http://minio:9000` on the connector forces the in-network endpoint regardless of what `.env` holds (which is aimed at host/real-vStorage runs). Related: [[MinIO server creds (ROOT_USERPASSWORD) are distinct env vars from the S3 client creds (ACCESS_KEYSECRET_KEY)]].

## Related

- [[MinIO server creds (ROOT_USERPASSWORD) are distinct env vars from the S3 client creds (ACCESS_KEYSECRET_KEY)]]

%% ai-graph-start %%

**Related notes:**
- [[MinIO server creds (ROOT_USERPASSWORD) are distinct env vars from the S3 client creds (ACCESS_KEYSECRET_KEY)]]
- [[Two Dockerfiles differing only in entrypoint should be one image plus compose override]]
- [[Docker hostname for reaching a service depends on where the caller runs]]
- [[AppsFlyer connector S3 config is vStorage-only - VSTORAGE_ env vars]]
- [[Relocating docker-compose.yml renames the Compose project and orphans volumes]]

%% ai-graph-end %%