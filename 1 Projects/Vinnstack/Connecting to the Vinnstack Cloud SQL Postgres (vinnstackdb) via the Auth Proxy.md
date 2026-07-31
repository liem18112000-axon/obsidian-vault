---
title: "Connecting to the Vinnstack Cloud SQL Postgres (vinnstackdb) via the Auth Proxy"
created: 2026-07-02
type: howto
status: seedling
source: "session 2026-07-02"
tags: [vinnstack, cloud-sql, postgres, gcp, proxy]
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
