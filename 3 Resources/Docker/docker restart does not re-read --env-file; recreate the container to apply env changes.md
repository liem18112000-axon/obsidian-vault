---
title: "docker restart does not re-read --env-file; recreate the container to apply env changes"
created: 2026-08-23
type: lesson
status: seedling
source: "leo-customer360 deployments/monitoring, session 2026-08-23"
tags: [docker, env-file, opentelemetry, jaeger, gotcha, leo-customer360]
---

# docker restart does not re-read --env-file; recreate the container to apply env changes

**`docker restart` does NOT re-read `--env-file`.** The variables from `docker run --env-file f` are read by the Docker CLI at create time and baked into the container config; `docker restart` just stops+starts the SAME container, reusing that baked env. So editing the env-file on disk and running `docker restart` leaves the OLD values live.

**How it bit us:** the leo-customer360 monitoring README documented a tracing 'fast path' — `sed -i s/OTEL_SDK_DISABLED=.../false/ /opt/c360/api.env && docker restart customer360-api`. It silently did nothing: after the sed+restart, `docker inspect --format '{{.Config.Env}}'` still showed `OTEL_SDK_DISABLED=true`, and the service never emitted spans.

**Fix:** RE-CREATE the container so `--env-file` is re-read, reusing the same image:
```
img=$(docker inspect <name> --format '{{.Config.Image}}')
docker rm -f <name>
docker run -d --name <name> <same flags...> --env-file <file> "$img"
```
Or just redeploy via the deploy script (which does rm+run).

**Verify the LIVE env, not the file:** `docker inspect <name> --format '{{range .Config.Env}}{{println .}}{{end}}'` shows what the process actually has — the file on disk can differ from the running container.

**Related Jaeger fact:** Jaeger lists a service in its dropdown only AFTER it receives >=1 span, and (with badger/persistent storage) RETAINS that service name from past spans. So a service can appear in Jaeger while currently disabled (historical spans), and an enabled-but-idle service won't appear until it gets traffic. That combination made it look like 'only customer360-api is instrumented' when all three were instrumented but off.

Source: leo-customer360 deployments/monitoring, enabling OTEL on api/ads/frontend (2026-08).
