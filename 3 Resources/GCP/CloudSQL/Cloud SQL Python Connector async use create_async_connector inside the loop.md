---
title: "Cloud SQL Python Connector async: use create_async_connector inside the loop"
created: 2026-08-30
type: howto
status: seedling
source: "session 2026-08-30 task-store verify"
tags: [gcp, cloud-sql, python, asyncpg, gotcha]
---

# Cloud SQL Python Connector async: use create_async_connector inside the loop

The Cloud SQL **Python** Connector lets you reach a Cloud SQL instance from Python over the IAM-authenticated Auth-proxy path using your gcloud ADC — no `cloud-sql-proxy` binary, no `psql`, and no public-IP exposure (works even with an instance that has no authorized networks). Install `cloud-sql-python-connector[asyncpg]`.

Async gotcha: create the connector with the awaited factory **inside the running loop**, not the plain `Connector()` constructor. `Connector()` binds to whatever loop exists at construction; calling `connect_async()` from a different loop raises `ConnectorLoopError: Running event loop does not match connector._loop`. Use:

```python
from google.cloud.sql.connector import create_async_connector, IPTypes
connector = await create_async_connector()          # inside async def, running loop
conn = await connector.connect_async(
    "PROJECT:REGION:INSTANCE", "asyncpg",
    user=u, password=pw, db=d, ip_type=IPTypes.PUBLIC)   # returns a raw asyncpg conn
# ... conn.fetch(...) ...
await conn.close(); await connector.close_async()
```

Pull the password from Secret Manager inside the script so it is never printed/hardcoded. Related: [[Cloud Run to Cloud SQL via Auth-proxy unix socket with asyncpg]].

## Related

- [[Cloud Run to Cloud SQL via Auth-proxy unix socket with asyncpg]]
