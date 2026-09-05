---
title: "docker compose run --rm SERVICE CMD overrides the service command for a one-shot"
created: 2026-08-17
type: lesson
status: seedling
source: "session 2026-08-17 manage-c360 backup target"
tags: [docker, docker-compose, technique]
---

# docker compose run --rm SERVICE CMD overrides the service command for a one-shot

A Compose service defined as a long-running sidecar (e.g. `restart: unless-stopped` + a cron/sleep-loop `command:`) can be reused for a **one-shot** run without a second service definition: `docker compose -f base.yml -f overlay.yml run --rm <service> <cmd>`. The `<cmd>` after the service name **overrides the service `command:`**, so the loop is replaced and the container runs once then (`--rm`) is removed.

Example: a `pg-backup` sidecar whose `command` is a daily sleep-loop is triggered on demand with `run --rm pg-backup /usr/local/bin/pg_backup.sh`. Because its `entrypoint` is `["/bin/sh","-c"]`, the override becomes `sh -c "/usr/local/bin/pg_backup.sh"` — one execution.

Notes: `run` ignores the service `container_name` (generates a one-off name) and the `restart:` policy, but still honors `depends_on`/healthchecks and the service env/volumes — so the one-shot lands in the same named volume as the scheduled runs. Used to build the `./manage-c360.sh backup` target. Related: [[Docker Compose command blocks need $$ to defer variable expansion to the container shell]].

## Related

- [[Docker Compose command blocks need $$ to defer variable expansion to the container shell]]
