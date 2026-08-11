---
ai_hash: 43fd237fb10a149d
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-02
entities:
- Vinnstack Cloud SQL Postgres
- vinnstackdb
- Cloud SQL for PostgreSQL 18
- Interrogation Room persistence work
- klara-nonprod:europe-west6:vinnstackdb
- klara-nonprod
- europe-west6
- postgres (database)
- vinnstack (database)
- postgres (user)
- Public IP 34.65.246.52
- Cloud SQL Auth Proxy (v2)
- gcloud ADC
- roles/cloudsql.client
- Windows
- curl.exe
- cloud-sql-proxy.exe
- 127.0.0.1:15432
- DATABASE_URL
- psql
- Cloud SQL Studio
- Installed Client
- Node `pg` script
- gcloud ACTIVE account
- Migrating Vinnstack Interrogation Room from JSON files to normalized Postgres (design)
source: session 2026-07-02
status: seedling
tags:
- vinnstack
- cloud-sql
- postgres
- gcp
- proxy
title: Connecting to the Vinnstack Cloud SQL Postgres (vinnstackdb) via the Auth Proxy
type: howto
---

# Connecting to the Vinnstack Cloud SQL Postgres (vinnstackdb) via the Auth Proxy

Vinnstack has a managed **Cloud SQL for PostgreSQL 18** instance backing the Interrogation Room persistence work:

- Instance / connection name: `klara-nonprod:europe-west6:vinnstackdb` (project klara-nonprod, region europe-west6).
- Databases: `postgres` (default) and **`vinnstack`** (the app target). Built-in user `postgres` (password auth; no IAM DB users).
- Public IP `34.65.246.52` is enabled, but the right way to connect locally is the **Cloud SQL Auth Proxy** (v2): it authenticates with your gcloud ADC (needs role roles/cloudsql.client on the project) — no IP allowlisting, no manual TLS.

Local dev recipe (Windows):
1. Download once: `curl.exe -o cloud-sql-proxy.exe https://storage.googleapis.com/cloud-sql-connectors/cloud-sql-proxy/v2.14.1/cloud-sql-proxy.x64.exe`
2. Run: `.\cloud-sql-proxy.exe klara-nonprod:europe-west6:vinnstackdb --port 15432` → listens on 127.0.0.1:15432 and tunnels to the instance.
3. App: `DATABASE_URL=postgresql://postgres:<PASSWORD>@127.0.0.1:15432/vinnstack`.

Gotchas: the proxy exe is ~32 MB — gitignore it. `psql` is NOT installed on the dev box, so apply schema via Cloud SQL Studio, an installed client, or a Node `pg` script. Multiple Google accounts: the gcloud ACTIVE account (not the browser authuser=N) is what the proxy uses — `gcloud config set account <you>` if the proxy hits permission denied.

## Related

- [[Migrating Vinnstack Interrogation Room from JSON files to normalized Postgres (design)]]

%% ai-graph-start %%

**Related notes:**
- [[Deploying a stateful single-tenant app to GKE with a Cloud SQL proxy sidecar]]
- [[Cloud SQL Auth Proxy needs roles-cloudsql.client on the connecting identity or it 403s NOT_AUTHORIZED]]
- [[Vinnstack Cloud Build trigger lives in klara-infra, not klara-nonprod]]
- [[Migrating Vinnstack Interrogation Room from JSON files to normalized Postgres (design)]]
- [[Vinnstack bundles cloud-sql-proxy.exe as a gitignored extraResource]]

**Relations:**
- Vinnstack Cloud SQL Postgres — *is also known as* — vinnstackdb
- Vinnstack Cloud SQL Postgres — *is an instance of* — Cloud SQL for PostgreSQL 18
- Cloud SQL for PostgreSQL 18 — *backs* — Interrogation Room persistence work
- klara-nonprod:europe-west6:vinnstackdb — *is instance name for* — Vinnstack Cloud SQL Postgres
- klara-nonprod:europe-west6:vinnstackdb — *is in project* — klara-nonprod
- klara-nonprod:europe-west6:vinnstackdb — *is in region* — europe-west6
- Vinnstack Cloud SQL Postgres — *has database* — postgres (database)
- Vinnstack Cloud SQL Postgres — *has database* — vinnstack (database)
- postgres (database) — *is* — default
- vinnstack (database) — *is* — app target
- Vinnstack Cloud SQL Postgres — *has user* — postgres (user)
- postgres (user) — *uses authentication method* — password auth
- Public IP 34.65.246.52 — *is enabled for* — Vinnstack Cloud SQL Postgres
- Cloud SQL Auth Proxy (v2) — *is recommended connection method for* — Vinnstack Cloud SQL Postgres
- Cloud SQL Auth Proxy (v2) — *authenticates with* — gcloud ADC
- gcloud ADC — *needs role* — roles/cloudsql.client
- roles/cloudsql.client — *applies to project* — klara-nonprod
- curl.exe — *downloads* — cloud-sql-proxy.exe
- cloud-sql-proxy.exe — *listens on* — 127.0.0.1:15432
- cloud-sql-proxy.exe — *tunnels to* — klara-nonprod:europe-west6:vinnstackdb
- DATABASE_URL — *connects to* — 127.0.0.1:15432
- psql — *is not installed on* — Windows
- Cloud SQL Studio — *can apply schema* — Vinnstack Cloud SQL Postgres
- Installed Client — *can apply schema* — Vinnstack Cloud SQL Postgres
- Node `pg` script — *can apply schema* — Vinnstack Cloud SQL Postgres
- Cloud SQL Auth Proxy (v2) — *uses* — gcloud ACTIVE account
- Migrating Vinnstack Interrogation Room from JSON files to normalized Postgres (design) — *is related to* — Vinnstack Cloud SQL Postgres
- Migrating Vinnstack Interrogation Room from JSON files to normalized Postgres (design) — *is related to* — Interrogation Room persistence work

%% ai-graph-end %%