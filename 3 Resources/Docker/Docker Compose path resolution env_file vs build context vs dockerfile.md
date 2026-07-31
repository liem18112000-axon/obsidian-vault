---
title: "Docker Compose path resolution: env_file vs build context vs dockerfile"
created: 2026-06-13
type: concept
status: seedling
source: "session 2026-06-13 accesstrade_integration"
tags: [docker, docker-compose, build-context, reference]
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
