---
title: "Running the LEO CDP GHCR image needs mounted configs (image ships JARs only)"
created: 2026-06-06
type: howto
status: seedling
source: "leo-cdp-framework docker-leocdp 2026-06-06"
tags: [leo-cdp, docker, docker-compose, deployment, gotcha]
---

# Running the LEO CDP GHCR image needs mounted configs (image ships JARs only)

The published `ghcr.io/trieu/leo-cdp-framework` image is **not runnable standalone** — `/app` contains only the four starter JARs (`leo-main-starter-docker.jar`, observer, scheduler, data-processing) + `deps/` + `resources/` + `public/`. It has **no `configs/` and no `leocdp-metadata.properties`** (the Gradle config-copy excludes env-specific files and the metadata file is gitignored). The repos intended deployment was VM-based (`devops-script/shell-script-starter/start-*.sh` reading configs on the host), so there was no app docker-compose.

To run it via docker-compose (verified working — admin serves HTTP 200 on `/`, `/login`, `/ping` at `0.0.0.0:9070`), mount a runtime config set over `/app` and inject DB creds via env. Gotchas, each found by iterating on a real boot error:
1. **DB creds via env:** set `mainDatabaseConfig=SYSTEM_ENV_VARS` in `leocdp-metadata.properties`, pass `ARANGODB_HOST/PORT/USERNAME/PASSWORD/DATABASE`. (A `configs/[PRO-]database-configs.json` must still *exist* — the loader reads the file before applying env.)
2. **Host bind:** `server.listen(port, host)` binds the configured host; the repo sample uses `cdpsys.admin` (wont bind in a container) → set the worker host to `0.0.0.0` in `http-routing-configs.json`.
3. **Redis:** the app needs BOTH `configs/redis-configs.json` (host/port/auth) AND `configs/redis-connection-pool-configs.json` (rfx-core pool tuning) — point redis-configs at the redis service, empty `auth` for a no-auth redis.
4. **UA parser:** every HTTP request parses the user-agent via rfx-core, which reads `configs/regexes.yaml` — missing it makes each request throw and reset the connection (curl shows HTTP 000 even though the server is listening). Ship the repos `configs/` wholesale to avoid one-missing-file-at-a-time.
5. **First run:** `docker compose run --rm <svc> setup-system-with-password <pw>` creates collections + super-admin.
6. **Image tag:** `:latest` only exists after a `main` release; feature-branch builds are tagged with the commit SHA only.

Bug observed in image `5f688f0`: `setup-system-with-password` exits 1 (after creating collections + admin) because `JourneyMapManagement.initDefaultSystemData()` seeds 1 touchpoint hub but `JourneyMap.setTouchpointHubsForJourneyMap` requires >=2. Non-fatal to running; default journey map just isnt seeded.

Deploy folder created: `core-leo-cdp/devops-script/docker-leocdp/`.

## Related
- [[Same-repo branch push fires both push and pull_request events (duplicate CI runs)]]
- [[LEO CDP CI provisions deps CI-natively, pinned to devops-script versions for parity]]

## Related

- [[1 Projects/leo-cdp/framework/LEO CDP CI provisions deps CI-natively, pinned to devops-script versions for parity]]
