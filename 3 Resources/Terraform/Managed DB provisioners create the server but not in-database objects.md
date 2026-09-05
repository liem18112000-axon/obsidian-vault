---
title: "Managed DB provisioners create the server but not in-database objects"
created: 2026-08-09
type: lesson
status: seedling
source: "session 2026-08-09 leo-customer360 terraform adapt"
tags: [terraform, postgres, iac, gotcha, vngcloud]
---

# Managed DB provisioners create the server but not in-database objects

A managed-database service provisioner (VNG Cloud vDB, AWS RDS, etc.) creates the database **server/instance** but does **not** manage objects *inside* the database. Extensions (`CREATE EXTENSION`), roles, grants, the schema, seed data, and any *additional* databases on the same server (e.g. a dedicated `db_keycloak` for Keycloak) are all out of scope for the provider resource.

**Why it matters:** IaC that only stands up the instance leaves a database the app cannot actually use — missing extensions, missing RLS role, missing side databases referenced by JDBC URLs. Those in-database objects must be applied by a separate bootstrap step: a `psql` local-exec, a CI/CD migration job, or a Kubernetes Job running idempotent SQL.

**Gotcha found in LEO Customer360:** terraform provisioned the Postgres instance + schema + RLS role but never created `db_keycloak`, even though every non-terraform path (docker-compose `keycloak-db-init`, the k8s postgres init image) did, and the app targets it via `KC_DB_URL`. Fix was to add its creation to the `db-bootstrap` module.

See [[Postgres RLS is silently bypassed by superuser connections]] and [[Idempotent CREATE DATABASE needs psql gexec since it cannot run in a transaction]].

## Related

- [[Postgres RLS is silently bypassed by superuser connections]]
- [[Idempotent CREATE DATABASE needs psql gexec since it cannot run in a transaction]]
