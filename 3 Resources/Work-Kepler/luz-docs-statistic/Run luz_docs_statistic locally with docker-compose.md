---
title: "Run luz_docs_statistic locally with docker-compose"
created: 2026-06-11
type: howto
status: seedling
source: "session 2026-06-11"
tags: [luz-docs-statistic, docker-compose, local-dev, wildfly]
---

# Run luz_docs_statistic locally with docker-compose

To run the luz_docs_statistic microservice locally via docker-compose, three things must be in place, in order:

1. **Build the WAR first** — the Dockerfile has no build stage; it `COPY`s `target/luz_docs_statistic.war` from the host. So run `mvn clean package -DskipUTs=true` (project-specific skip flag) before any `docker-compose build`.
2. **Dependencies reachable on `host.docker.internal:8080`** — the compose file points luz_jsonstore, luzsec and luz_cache at that address, so a `kubectl port-forward services/api-forwarder 8080` into the dev namespace must be running (same pattern as the luz-docs-local-run skill).
3. **Start it**: `docker-compose up -d --no-deps --build luz-docs-statistic`.

Published ports: app on **localhost:8199** (context `/luz_docs_statistic/api`), JVM debug on 8787, WildFly management on 9990.

Smoke test (no auth needed): `GET http://localhost:8199/luz_docs_statistic/api/version` returns `{"luz_docs_statistic":"<version>"}`.

## Related
- [[Local luz-docs and luz_docs_statistic both bind host ports 8787 and 9990]]
