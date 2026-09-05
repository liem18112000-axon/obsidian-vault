---
title: "kind image pulls stall on Docker Hub rate limits; pre-pull and kind load"
created: 2026-08-03
type: lesson
status: seedling
source: "session 2026-08-03"
tags: [kind, kubernetes, dockerhub, imagepull, ratelimit, gotcha]
---

# kind image pulls stall on Docker Hub rate limits; pre-pull and kind load

On a `kind` cluster, pods that use PUBLIC images (bitnami/kafka, minio, postgres:alpine, etc.) can sit in `ContainerCreating` → `ErrImagePull`/`ImagePullBackOff` for many minutes when Docker Hub **rate-limits anonymous pulls** — the event just shows "Pulling image" with no error, and even tiny images (alpine) stall while an unrelated one occasionally gets through. It is an environment/network limit, not a manifest bug.

Fixes, most reliable first:
- `docker login` (authenticated Docker Hub pulls have far higher limits), then re-pull.
- **Pre-pull on the host + `kind load docker-image <img> --name <cluster>`** so kubelet finds the image already present (bypasses the in-node pull). Verify with `docker exec <cluster>-control-plane crictl images | grep <img>`. Caveat: a pre-pull loop that lacks `set -e` will exit 0 even if a pull failed — check crictl, don't trust the job's exit code.
- Locally-built app images must ALSO be `kind load`ed (kind's node containerd is separate from the host Docker daemon).

Related k8s-vs-compose gotcha hit in the same run: compose `depends_on: service_healthy` has NO Kubernetes equivalent, so one-shot seed/init pods race their dependencies — add an initContainer that blocks on readiness (e.g. `until pg_isready ...`).

## Related
- [[Customer360 Kubernetes deployment (local kind + GreenNode VKS)]]
- [[Kustomize component makes a service tier optional per environment]]
