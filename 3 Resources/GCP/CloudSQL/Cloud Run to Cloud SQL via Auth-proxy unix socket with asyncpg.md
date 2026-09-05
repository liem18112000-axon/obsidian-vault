---
title: "Cloud Run to Cloud SQL via Auth-proxy unix socket with asyncpg"
created: 2026-08-29
type: howto
status: seedling
source: "session 2026-08-29 task-store migration"
tags: [gcp, cloud-run, cloud-sql, asyncpg, sqlalchemy, terraform]
---

# Cloud Run to Cloud SQL via Auth-proxy unix socket with asyncpg

Cloud Run (v2) reaches a Cloud SQL instance through the built-in **Cloud SQL Auth proxy**, which the runtime mounts as a **unix socket directory** at `/cloudsql/<PROJECT:REGION:INSTANCE>`. With asyncpg you connect by passing that directory as the `host` (no port): a leading `/` tells asyncpg to use a socket, and the proxy handles TLS + IAM.

The officially documented SQLAlchemy form is to put the socket dir in the `host` **query** param, not the netloc:

```python
from sqlalchemy import URL
URL.create("postgresql+asyncpg",
           username=user, password=pw, database=db,
           query={"host": "/cloudsql/PROJECT:REGION:INSTANCE"})
# -> postgresql+asyncpg://user:pw@/db?host=%2Fcloudsql%2FPROJECT%3AREGION%3AINSTANCE
```

Terraform wiring (Cloud Run v2): a `volumes { cloud_sql_instance { instances = [instance.connection_name] } }` block, a matching `volume_mounts { mount_path = "/cloudsql" }` in the container, and `roles/cloudsql.client` on the runtime SA. The instance keeps a public IP with **no authorized_networks** — nothing connects over raw IP; only the IAM-authenticated proxy can.

See [[a2a-sdk DatabaseTaskStore makes A2A tasks survive Cloud Run restarts]] and [[SQLAlchemy URL.create encodes passwords correctly; hand-encoding with quote_plus can corrupt them]].

## Related

- [[a2a-sdk DatabaseTaskStore makes A2A tasks survive Cloud Run restarts]]
- [[SQLAlchemy URL.create encodes passwords correctly; hand-encoding with quote_plus can corrupt them]]
