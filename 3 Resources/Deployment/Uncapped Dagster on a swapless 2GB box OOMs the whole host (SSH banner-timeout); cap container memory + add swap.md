---
title: "Uncapped Dagster on a swapless 2GB box OOMs the whole host (SSH banner-timeout); cap container memory + add swap"
created: 2026-08-23
type: lesson
status: seedling
source: "leo-customer360 UAT backend box, session 2026-08-23"
tags: [oom, memory, dagster, docker, incident, leo-customer360]
---

# Uncapped Dagster on a swapless 2GB box OOMs the whole host (SSH banner-timeout); cap container memory + add swap

**Incident:** the tiny UAT backend box (s-general-1x2, 1 vCPU / 2 GB, key 1x2, ins-14fd1b33…) running **Dagster** (backend-system) became fully unreachable — SSH hung at the banner on :22, and every port (incl. LB→Dagster :3000) timed out. Looked like a Dagster/LB problem; it was a **host-level OOM**.

**Root cause (confirmed post-reboot):** Dagster's container used ~**1.5 GiB of 2 GiB** (78%), the box had **no swap** and ~90 MB free. A memory spike drove the host OOM; the kernel thrashed and sshd couldn't even send its banner. The Portainer agent I'd added (32 MiB) was NOT the cause.

**Diagnostic heuristic:** *TCP connects but SSH hangs at the banner, AND all the box's ports (not just the app) are dead, AND the LB member is down* = a HOST-level outage (OOM / overload / crash), not an app or LB-config problem. SSH is direct to the floating IP, so if SSH is dead too, it's the box. Confirm after recovery with `uptime` + `free -m` + `docker stats --no-stream`.

**Recovery:** when SSH is dead there's no remote fix — reboot the instance from the cloud console. Containers with `--restart unless-stopped` come back on boot (Dagster + agent did). A rename in the console is cosmetic — same instance ID = same box/disk, not recreated.

**Prevention (key insight):** an UNCAPPED memory-hungry container on a swapless small box takes the WHOLE HOST down on OOM (unrecoverable without a console reboot). Fixes: (1) set a container `--memory` cap so Docker OOM-kills just that container (recoverable via restart-policy) instead of the host locking up; (2) add swap (e.g. a 2 GB swapfile) for headroom on spikes; (3) right-size / move the heavy service long-term. Cap + swap converts an unrecoverable host lockup into a recoverable container restart.

**Meta-gotcha (how this note first got corrupted):** backticks inside a double-quoted shell string are command-substituted — `printf '%s' "... \`free -m\` ..."` executed `free -m` etc. and injected their output. When embedding shell/backtick text into a note body from the CLI, use a single-quoted here-doc (`<<'BODY'`) so nothing expands.

Source: leo-customer360 UAT backend box outage, 2026-08-23.

## Related

- [[docker restart does not re-read --env-file; recreate the container to apply env changes]]
