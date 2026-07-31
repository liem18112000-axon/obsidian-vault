---
ai_hash: c79e08de865e233f
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-13
entities: []
source: session 2026-06-13 accesstrade_integration
status: seedling
tags:
- docker
- docker-compose
- build-context
- reference
title: 'Docker Compose path resolution: env_file vs build context vs dockerfile'
type: concept
---

# Docker Compose path resolution: env_file vs build context vs dockerfile

When a `docker-compose.yml` does not sit at the repo root, three different path bases apply — getting them wrong gives "file not found" or a silently broken ignore list:

- **`env_file` and bind-mount source paths** resolve relative to the **compose file's own directory**. So a repo-root `.env` referenced from `dockers/docker-compose.yml` must be `../.env`, not `.env`.
- **`build.context`** is relative to the **compose file's directory** too (use `context: ..` to point at the repo root).
- **`build.dockerfile`** is relative to the **build `context`**, NOT the compose file. With `context: ..`, you write `dockerfile: dockers/web.Dockerfile`.
- **`.dockerignore`** resolves relative to the **build context**, not the Dockerfile. With `context: ..` it must stay at the repo root to take effect; a copy next to the Dockerfile is ignored.

```yaml
services:
  web:
    build:
      context: ..                      # repo root
      dockerfile: dockers/web.Dockerfile  # relative to context
    env_file: [../.env]                # relative to this compose file
```

Rule of thumb: **context-relative** = dockerfile + .dockerignore; **compose-file-relative** = env_file + context + bind mounts. Pair with [[Relocating docker-compose.yml renames the Compose project and orphans volumes]].

## Related

- [[Relocating docker-compose.yml renames the Compose project and orphans volumes]]

%% ai-graph-start %%

**Related notes:**
- [[Relocating docker-compose.yml renames the Compose project and orphans volumes]]
- [[Docker hostname for reaching a service depends on where the caller runs]]
- [[BuildKit honors a per-Dockerfile .dockerignore]]
- [[Path(__file__).parent breaks when a module is moved to a deeper directory]]
- [[Separate docker-compose files are isolated networks; use one file + a profile for optional services]]

%% ai-graph-end %%