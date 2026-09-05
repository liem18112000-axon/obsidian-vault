---
title: "Customer360 redis.conf omits port so redis listens on 6379 not 6580"
created: 2026-08-03
type: lesson
status: seedling
source: "session 2026-08-03"
tags: [redis, kubernetes, config, port, gotcha, customer360]
---

# Customer360 redis.conf omits port so redis listens on 6379 not 6580

The Customer360 `redis/redis.conf` sets `appendonly`, `maxmemory`, etc. but has **no `port` directive**, so the redis-server actually listens on the default **6379** — even though the Dockerfile `EXPOSE 6580`, the compose port map, and the app's `REDIS_PORT` all assume **6580**. `EXPOSE` is documentation only; it does not change the listen port.

Symptom in Kubernetes: a readiness probe / Service targeting 6580 never passes (`redis-cli -p 6580 ping` refused), pod stays `0/1`, and the app's cache silently "fails open" to Postgres so the misconfig is easy to miss. The redis log line `Running mode=standalone, port=6379` is the tell.

Fix: make redis listen on 6580 — either add `port 6580` to `redis.conf` (fixes docker-compose too, needs image rebuild) or pass `--port 6580` as a container arg (fixes k8s without a rebuild, since args override the conf). The k8s manifest uses the `--port 6580` arg.

## Related
- [[Customer360 Kubernetes deployment (local kind + GreenNode VKS)]]
