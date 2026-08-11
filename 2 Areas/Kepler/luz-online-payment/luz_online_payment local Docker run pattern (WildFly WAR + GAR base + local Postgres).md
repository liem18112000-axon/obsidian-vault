---
ai_hash: 23c8ee7f8eb7a824
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-29
entities: []
source: session 2026-07-29; docs/LOCAL-RUN.md
status: seedling
tags:
- luz
- docker
- wildfly
- local-run
- postgres
- flyway
title: luz_online_payment local Docker run pattern (WildFly WAR + GAR base + local
  Postgres)
type: howto
---

# luz_online_payment local Docker run pattern (WildFly WAR + GAR base + local Postgres)

luz WildFly services (e.g. luz_online_payment) run locally via docker-compose with this shape:

1. The Dockerfile is thin: it `COPY target/<artifact>.war` onto a private base image `europe-west6-docker.pkg.dev/klara-repo/.../luz-wildfly26-all`. So you must (a) `gcloud auth configure-docker europe-west6-docker.pkg.dev` to pull the base image, and (b) build the WAR first with `mvn clean install -Dmaven.test.skip=true` before `docker compose up --build`.
2. DB schema is NOT pre-baked: the app runs Flyway-style migrations from `src/main/resources/db/public` + `db/migration` (files named `V<version>__desc.sql`) against an EMPTY database at startup. So a plain empty Postgres with the right db name is enough.
3. WildFly datasource uses `prefill=true`, so it connects at boot — the DB must be up first. In compose, gate the app on a Postgres `healthcheck` via `depends_on: condition: service_healthy`.
4. Cross-service integrations point at `host.docker.internal:8080` (JWT/luzsec, luz_compensation, luz_online, luz_eletter). They are called lazily at runtime, so the app boots without them; run the real services or a `kubectl port-forward services/api-forwarder 8080` to the dev cluster for full functionality.
5. luz_online_payment local ports: app 8128->8080, debug 8788, bundled Postgres 6666->5432. App base path `/luz_online_payment/api`.

See [[1 Projects/luz_store/LUZ-157476/LUZ-157476 decline-code flow luz-online-payment forwards, luz_store maps]] for the feature context.

## Related

- [[1 Projects/luz_store/LUZ-157476/LUZ-157476 decline-code flow luz-online-payment forwards, luz_store maps]]

%% ai-graph-start %%

**Related notes:**
- [[Run luz_docs_statistic locally with docker-compose]]
- [[Local luz-docs and luz_docs_statistic both bind host ports 8787 and 9990]]
- [[Luz performance env cluster topology]]
- [[Shipping luz_docs_statistic trigger is docs-statistic-service and dev runs a Deployment, not a StatefulSet]]
- [[Running the LEO CDP GHCR image needs mounted configs (image ships JARs only)]]

%% ai-graph-end %%