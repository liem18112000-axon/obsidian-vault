---
title: "pgBackRest archive_command needs the binary IN the postgres image; the scheduler is a separate sidecar"
created: 2026-08-16
type: lesson
status: seedling
source: "session 2026-08-16 physical track"
tags: [pgbackrest, postgres, docker, kubernetes, backup]
---

# pgBackRest archive_command needs the binary IN the postgres image; the scheduler is a separate sidecar

PostgreSQL executes `archive_command` **inside the server process**, so for `archive_command = pgbackrest ... archive-push %p` the **pgbackrest binary must be installed in the same image/host as the postgres server** — not only in a separate backup container. (In LEO CDP this meant adding `pgbackrest` to `postgres/Dockerfile`, alongside pgvector.)

The scheduled base backups (`pgbackrest backup` + `expire`) are a **different** concern and run well from a **sidecar** using the same image, provided it shares (a) the `PGDATA` volume/PVC and (b) the postgres **unix socket** dir (`/var/run/postgresql`, an emptyDir in K8s / a named volume in Compose). The sidecar reads data files locally and opens the control connection over the shared socket.

So the split is: **archive-push = in the DB container**; **backup/expire = sidecar (shared PGDATA + socket)**. Run the sidecar as the `postgres` user so it matches PGDATA ownership. Keep secrets out of `pgbackrest.conf` via `PGBACKREST_*` env vars (e.g. `PGBACKREST_REPO1_S3_KEY`, `PGBACKREST_REPO1_CIPHER_PASS`). Related: [[pgBackRest runs on the DB host, so it cannot back up a managed DB like VNG vDB]].

## Related

- [[pgBackRest runs on the DB host]]
- [[so it cannot back up a managed DB like VNG vDB]]
