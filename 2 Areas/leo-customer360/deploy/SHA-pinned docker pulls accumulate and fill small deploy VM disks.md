---
title: "SHA-pinned docker pulls accumulate and fill small deploy VM disks"
created: 2026-09-04
type: lesson
status: seedling
source: "session 2026-09-04, GH Actions run 33886578303"
tags: [leo-customer360, docker, deploy, disk-space, gotcha]
---

# SHA-pinned docker pulls accumulate and fill small deploy VM disks

Each deploy in the leo-customer360 vServer scripts (`deployments/server/deploy-*.sh`) does a SHA-pinned `docker pull` of a new image but only `docker rm -f` the container — it never removes old images. Over many deploys the stale SHA-pinned images pile up and eventually fill a small VM's disk. The failure surfaces on the *next* deploy at the very first disk write — `cat: write error: No space left on device` while writing the env file — not as an obvious image/registry error, which makes it easy to misread as a code bug.

**Why it happens:** pinning to `@sha256:...` means every build produces a distinct image reference; none share a mutable tag, so nothing is ever overwritten/GC'd automatically.

**Fix:** add a best-effort reclaim step to each deploy (`docker container prune -f`; `docker image prune -a -f`; `docker builder prune -a -f`). `image prune -a` is safe to run mid-deploy because the currently-running container still holds its image at that moment, so only stale images are dropped.

Seen in a GitHub Actions deploy job (uat, api VM) on 2026-09-04.

## Related

- [[Prune disk before any write in an SSH heredoc so it works on a full disk]]
