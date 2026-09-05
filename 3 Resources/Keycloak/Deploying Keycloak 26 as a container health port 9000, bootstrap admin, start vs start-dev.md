---
title: "Deploying Keycloak 26 as a container: health port 9000, bootstrap admin, start vs start-dev"
created: 2026-08-19
type: howto
status: seedling
source: "leo-customer360 deploy-sso session 2026-08-19"
tags: [keycloak, sso, oidc, docker, ops, gotcha]
---

# Deploying Keycloak 26 as a container: health port 9000, bootstrap admin, start vs start-dev

Operational facts for running keycloak/keycloak:26.x as a plain `docker run` container
(not via the Keycloak operator/compose healthcheck):

- HEALTH endpoints moved to the MANAGEMENT port 9000, not the app port 8080. Enable
  with KC_HEALTH_ENABLED=true and poll `http://127.0.0.1:9000/health/ready`. Polling
  8080/health returns 404 and makes readiness checks look broken.
- ADMIN bootstrap in 26 uses KC_BOOTSTRAP_ADMIN_USERNAME / KC_BOOTSTRAP_ADMIN_PASSWORD.
  The older KEYCLOAK_ADMIN / KEYCLOAK_ADMIN_PASSWORD are deprecated (warn, may be removed).
- `start-dev` = dev mode: HTTP enabled, lenient hostname -- fine for uat/testing.
  `start` = production mode: refuses to boot without a hostname and TLS *unless* you set
  KC_HTTP_ENABLED=true (when TLS terminates upstream at a load balancer), plus
  KC_PROXY_HEADERS=xforwarded and a KC_HOSTNAME (KC_HOSTNAME_STRICT=false to be lenient).
- DB: KC_DB=postgres + KC_DB_URL=jdbc:postgresql://HOST:5432/DBNAME + KC_DB_USERNAME/PASSWORD.
  Keycloak creates its own ~100-table schema in that database on first start; the DB and a
  role with create privileges must already exist.
- CO-LOCATING on a small box: a JVM defaults its max heap to ~70% of the machine RAM, which
  will OOM neighbors on a 2 GB box already running other services. Cap it via
  JAVA_OPTS_APPEND="-Xms256m -Xmx512m". Verified: Keycloak + FastAPI + Redis coexist on a
  1 vCPU / 2 GB box that way (~958 MB still available after start).

Context: leo-customer360 deployments/sso/deploy-sso.sh (uat shares the api VM; prod = dedicated
vServer). Related: [[customer360-api has two auth modes dev local-JWT vs Keycloak SSO]]

## Related

- [[customer360-api has two auth modes dev local-JWT vs Keycloak SSO]]
