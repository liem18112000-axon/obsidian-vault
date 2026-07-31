---
title: "Docker hostname for reaching a service depends on where the caller runs"
created: 2026-06-14
type: lesson
status: seedling
source: "session 2026-06-14, accesstrade_integration compose"
tags: [docker, compose, networking, localhost, host-docker-internal, gotcha]
---

# Docker hostname for reaching a service depends on where the caller runs

**`localhost` inside a container means the container itself — so the hostname you use to reach a service (Redis, Postgres, …) depends on WHERE the calling process runs relative to the target. Three cases:**

1. **Host process → container (with published ports `-p`)**: use `localhost` (port-publishing maps host `localhost:port` into the container).
2. **Container → container on the same Docker/compose network**: use the **service name** as the host (e.g. `redis://redis:6379`, `postgres://db:5432`). NOT `localhost`.
3. **Container → host (or a service published on the host)**: use `host.docker.internal` (Docker Desktop / Rancher Desktop provide it; native Linux Docker needs `extra_hosts: ["host.docker.internal:host-gateway"]`).

**The shared-`env_file` pitfall:** if a compose service and a host-run process both read the same `.env`, a single `DATABASE_URL=...@localhost:...` can't be right for both — the host app reaches the published container, but the container would resolve `localhost` to itself (→ 'unreachable'). Fix: keep store URLs OUT of the shared `.env`; put container-scoped values (service-name hosts) in the compose service's `environment:` (which overrides `env_file`), and use `localhost` only for the host-run path. Or run everything in compose and use service names everywhere.

Wiring tip: give DB/cache services a `healthcheck` and make the app `depends_on: { db: { condition: service_healthy } }` so it doesn't start before they accept connections. Publish the DB/cache ports too if you also want a host-run app/CLI to reach them at `localhost`. Verified in accesstrade_integration's dockers/docker-compose.yml. Relates to [[Password-gate a server-rendered admin panel and show dependency-free store status]] (the admin panel's TCP probe is what surfaces a wrong host as 'unreachable').
