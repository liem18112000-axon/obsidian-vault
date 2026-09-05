---
title: "Docker json-file logs are unbounded; cap them with --log-opt on high-volume containers"
created: 2026-09-04
type: lesson
status: seedling
source: "session 2026-09-04"
tags: [leo-customer360, docker, logging, disk-space, nginx, gotcha]
---

# Docker json-file logs are unbounded; cap them with --log-opt on high-volume containers

Docker's default logging driver (`json-file`) keeps container stdout/stderr in `/var/lib/docker/containers/<id>/<id>-json.log` with **no size limit**. On a high-volume service this silently fills the host disk — a second disk-overload vector separate from image accumulation.

**Where it bit (leo-customer360 UAT):** the data-tracking vServer runs N `data-tracking-api` replicas behind an nginx LB. The LB writes an `access_log` line for *every* ingestion request (one per event), and neither the replicas nor the LB set any rotation → the json logs grow without bound and overload the UAT VM disk.

**Fix:** add rotation to each `docker run`: `--log-opt max-size=10m --log-opt max-file=3` (caps each container to ~30 MB). Define once as a `LOG_OPTS` var and reuse across the replica and LB run commands.

**Gotchas / notes:**
- `--log-opt` only affects **newly created** containers — it does not retroactively rotate an existing container's log. Because the tracking deploy `docker rm -f`'s and recreates every replica + LB, a redeploy both caps future logs and *discards* the already-bloated old json.log with the removed container.
- `docker container prune`/`image prune` do NOT reclaim a running container's log file — only removing/recreating the container does.
- Alternative global fix: set defaults in `/etc/docker/daemon.json` (`log-driver`/`log-opts`), but it needs a daemon restart and still only applies to newly created containers, so per-run flags are simpler here.

Related: [[SHA-pinned docker pulls accumulate and fill small deploy VM disks]].

## Related

- [[SHA-pinned docker pulls accumulate and fill small deploy VM disks]]
