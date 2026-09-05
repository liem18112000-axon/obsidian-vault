---
title: "Local Cloud SQL admin without psql: use the Python connector + asyncpg"
created: 2026-08-31
type: howto
status: seedling
source: "session 2026-08-31 clear_taskstore"
tags: [cloud-sql, asyncpg, python-connector, gcp, tooling, windows]
---

# Local Cloud SQL admin without psql: use the Python connector + asyncpg

For a small local script that administers a Cloud SQL Postgres (e.g. TRUNCATE a table) on a machine WITHOUT the psql client, skip psql + the Cloud SQL Auth Proxy binary entirely and connect from the project's own venv:

  from google.cloud.sql.connector import create_async_connector   # IAM auth via ADC
  connector = await create_async_connector()                       # MUST be created in the running loop
  conn = await connector.connect_async(INSTANCE_CONNECTION_NAME, 'asyncpg', user=..., password=..., db=...)

Why this beats psql+proxy for a repo tool: no external psql install, no proxy binary download/lifecycle, and it reuses deps the app already has (asyncpg + cloud-sql-python-connector ship with a2a-sdk[postgresql]). The connector authenticates to the instance over ADC (local identity needs roles/cloudsql.client); the Postgres-level auth is still user+password (fetch the password from Secret Manager, don't hardcode). Gotcha: `create_async_connector()` must be awaited INSIDE the running loop or you get ConnectorLoopError.

Discovered when test-agent/tools/clear_taskstore.sh first ran: psql wasn't on the Windows box, so the psql-based script was rewritten to this connector approach. Also: emit ASCII (not · / —) in tool output — the Windows console (cp1252) mojibakes UTF-8 punctuation. Related: [[Task store on Cloud SQL]] if present.
