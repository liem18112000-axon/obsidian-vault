---
title: "Cloud Run mounts the Cloud SQL /cloudsql socket only into the ingress container, not sidecars"
created: 2026-09-03
type: lesson
status: seedling
source: "session 2026-09-03 — test-agent taskstore"
tags: [gcp, cloud-run, cloud-sql, sidecar, gotcha, sqlalchemy]
---

# Cloud Run mounts the Cloud SQL /cloudsql socket only into the ingress container, not sidecars

On a **multi-container Cloud Run service**, Cloud Run's built-in Cloud SQL integration (the managed Auth-proxy that appears as a unix socket under **`/cloudsql/<INSTANCE_CONNECTION_NAME>`**) is mounted **only into the ingress container**, NOT into sidecar containers. A sidecar that tries to connect via that socket path gets **`FileNotFoundError`** (asyncpg: no such socket directory), because the socket simply isn't there.

**Fix:** the sidecar must reach Cloud SQL a different way — the **Cloud SQL Python Connector** (`cloud-sql-python-connector`) is the clean one: it opens an **IAM-authenticated TLS connection straight to the instance** over the container's normal egress, no socket involved. Needs `roles/cloudsql.client` + the Cloud SQL Admin API enabled. With SQLAlchemy async this is the documented shape:

```python
from google.cloud.sql.connector import create_async_connector, IPTypes
from sqlalchemy.ext.asyncio import create_async_engine

connector = None
async def getconn():
    global connector
    if connector is None:               # create lazily, inside the running loop
        connector = await create_async_connector()
    return await connector.connect_async(INSTANCE, "asyncpg", user=U, password=P, db=D,
                                          ip_type=IPTypes.PUBLIC)  # or PRIVATE

engine = create_async_engine("postgresql+asyncpg://", async_creator=getconn, pool_pre_ping=True)
```

**Gotchas:** create the Connector **lazily on first connection** (not at import/app-construction time) so it binds to the running event loop. Toggle public vs private IP with `IPTypes`. A plain-TCP path (local docker-compose / a manually-run proxy on `localhost`) still works via the normal SQLAlchemy URL — this only bites the managed-socket path in a sidecar.

Surfaced in the test-agent Cloud Run deployment (KGA/TPD agents run as sidecars behind an MCP-bridge ingress container); the task store's DatabaseTaskStore was switched from the `/cloudsql` socket to the Connector for exactly this reason.
