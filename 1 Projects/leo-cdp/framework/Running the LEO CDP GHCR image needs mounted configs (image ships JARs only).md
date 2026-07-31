---
ai_hash: 113a4f997e6a177e
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-06
entities:
- LEO CDP GHCR image
- ghcr.io/trieu/leo-cdp-framework
- /app
- starter JARs
- leo-main-starter-docker.jar
- observer JAR
- scheduler JAR
- data-processing JAR
- deps/
- resources/
- public/
- configs/
- leocdp-metadata.properties
- Gradle
- env-specific files
- devops-script/shell-script-starter/start-*.sh
- VM-based deployment
- docker-compose
- admin service
- HTTP 200
- /
- /login
- /ping
- 0.0.0.0:9070
- database credentials
- environment variables
- mainDatabaseConfig
- SYSTEM_ENV_VARS
- ARANGODB_HOST
- ARANGODB_PORT
- ARANGODB_USERNAME
- ARANGODB_PASSWORD
- ARANGODB_DATABASE
- configs/[PRO-]database-configs.json
- config loader
- server.listen(port, host)
- configured host
- cdpsys.admin
- container
- worker host
- 0.0.0.0
- http-routing-configs.json
- Redis
- configs/redis-configs.json
- configs/redis-connection-pool-configs.json
- rfx-core
- host/port/auth
- rfx-core pool tuning
- redis service
- authentication
- UA parser
- user-agent
- configs/regexes.yaml
- HTTP 000
- setup-system-with-password command
- collections
- super-admin
- :latest tag
- main release
- feature-branch builds
- commit SHA
- image 5f688f0
- JourneyMapManagement.initDefaultSystemData()
- JourneyMap.setTouchpointHubsForJourneyMap
- touchpoint hub
- core-leo-cdp/devops-script/docker-leocdp/
- Same-repo branch push fires both push and pull_request events (duplicate CI runs)
- LEO CDP CI provisions deps CI-natively, pinned to devops-script versions for parity
- 1 Projects/leo-cdp/framework/LEO CDP CI provisions deps CI-natively, pinned to devops-script
  versions for parity
source: leo-cdp-framework docker-leocdp 2026-06-06
status: seedling
tags:
- leo-cdp
- docker
- docker-compose
- deployment
- gotcha
title: Running the LEO CDP GHCR image needs mounted configs (image ships JARs only)
type: howto
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

%% ai-graph-start %%

**Related notes:**
- [[LEO CDP CI provisions deps CI-natively, pinned to devops-script versions for parity]]
- [[LEO CDP SYSTEM_ENV_VARS still requires database-configs.json to exist first]]
- [[Job-level defaults.run.working-directory breaks Initialize containers (pre-checkout)]]
- [[CI build Docker image on every run, push only on non-PR]]
- [[Trivy scan-before-push needs a single-arch load build first]]

**Relations:**
- LEO CDP GHCR image — *is identified by* — ghcr.io/trieu/leo-cdp-framework
- LEO CDP GHCR image — *ships* — JARs only
- LEO CDP GHCR image — *needs* — mounted configs
- LEO CDP GHCR image — *is not* — runnable standalone
- ghcr.io/trieu/leo-cdp-framework — *contains* — /app
- /app — *contains* — starter JARs
- starter JARs — *include* — leo-main-starter-docker.jar
- starter JARs — *include* — observer JAR
- starter JARs — *include* — scheduler JAR
- starter JARs — *include* — data-processing JAR
- /app — *contains* — deps/
- /app — *contains* — resources/
- /app — *contains* — public/
- LEO CDP GHCR image — *has no* — configs/
- LEO CDP GHCR image — *has no* — leocdp-metadata.properties
- Gradle — *excludes* — env-specific files
- Gradle — *excludes* — leocdp-metadata.properties
- leocdp-metadata.properties — *is* — gitignored
- repo — *intended deployment was* — VM-based deployment
- VM-based deployment — *uses* — devops-script/shell-script-starter/start-*.sh
- devops-script/shell-script-starter/start-*.sh — *reads configs on* — host
- docker-compose — *can run* — LEO CDP GHCR image
- LEO CDP GHCR image — *requires* — mounted configs
- LEO CDP GHCR image — *requires* — injected database credentials
- admin service — *serves* — HTTP 200
- HTTP 200 — *on* — / HTTP endpoint
- HTTP 200 — *on* — /login HTTP endpoint
- HTTP 200 — *on* — /ping HTTP endpoint
- admin service — *listens at* — 0.0.0.0:9070
- database credentials — *set via* — environment variables
- mainDatabaseConfig — *set to* — SYSTEM_ENV_VARS
- SYSTEM_ENV_VARS — *in* — leocdp-metadata.properties
- environment variables — *pass* — ARANGODB_HOST
- environment variables — *pass* — ARANGODB_PORT
- environment variables — *pass* — ARANGODB_USERNAME
- environment variables — *pass* — ARANGODB_PASSWORD
- environment variables — *pass* — ARANGODB_DATABASE
- configs/[PRO-]database-configs.json — *must* — exist
- config loader — *reads* — configs/[PRO-]database-configs.json
- server.listen(port, host) — *binds* — configured host
- repo sample — *uses* — cdpsys.admin
- cdpsys.admin — *wont bind in* — container
- worker host — *set to* — 0.0.0.0
- 0.0.0.0 — *in* — http-routing-configs.json
- app — *needs* — configs/redis-configs.json
- app — *needs* — configs/redis-connection-pool-configs.json
- configs/redis-configs.json — *contains* — host/port/auth
- configs/redis-connection-pool-configs.json — *contains* — rfx-core pool tuning
- configs/redis-configs.json — *points at* — redis service
- authentication — *is empty for* — no-auth redis
- UA parser — *parses* — user-agent
- UA parser — *via* — rfx-core
- rfx-core — *reads* — configs/regexes.yaml
- missing configs/regexes.yaml — *causes* — each request throw
- missing configs/regexes.yaml — *causes* — reset the connection
- setup-system-with-password command — *creates* — collections
- setup-system-with-password command — *creates* — super-admin
- :latest tag — *exists after* — main release
- feature-branch builds — *tagged with* — commit SHA
- image 5f688f0 — *has* — bug
- setup-system-with-password command — *exits 1 in* — image 5f688f0
- JourneyMapManagement.initDefaultSystemData() — *seeds* — 1 touchpoint hub
- JourneyMap.setTouchpointHubsForJourneyMap — *requires* — >=2 touchpoint hubs
- core-leo-cdp/devops-script/docker-leocdp/ — *is* — deploy folder

%% ai-graph-end %%