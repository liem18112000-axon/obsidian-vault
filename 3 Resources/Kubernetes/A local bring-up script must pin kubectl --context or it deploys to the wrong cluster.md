---
title: "A local bring-up script must pin kubectl --context or it deploys to the wrong cluster"
created: 2026-08-07
type: lesson
status: seedling
source: "session 2026-08-07 leo-customer360 up.sh"
tags: [kubectl, kubernetes, kind, gke, local-dev, gotcha, context]
---

# A local bring-up script must pin kubectl --context or it deploys to the wrong cluster

A shell script that runs **bare `kubectl`** (no `--context` / `--kubeconfig`) operates against whatever `kubectl config current-context` happens to be. For a script whose header claims "LOCAL bring-up" this is a silent footgun: if the user has since switched context to a remote cluster (e.g. a shared GKE non-prod), a plain `kubectl apply -k overlays/local` deploys the entire local-dev stack **into the remote cluster** instead of the local kind one — and it looks like it "worked" because most resources apply cleanly.

**How it surfaced (leo-customer360 `up.sh`):** `up.sh` created the kind cluster and `kind load`ed images correctly (those commands take `--name`, so they are context-independent), but its `kubectl apply -k` / `rollout status` / `get pods` had no `--context`. The user's current context was `gke_klara-nonprod_...`, so a whole `customer360` namespace got created in the shared GKE cluster (pods stuck in ImagePullBackOff, since the `*:local` images live only on the kind node). The tell was `kubectl get svc` showing the "existing" services aged **8 minutes**, not days.

**Fix — pin the context once and reuse it everywhere:**
```bash
CLUSTER="${KIND_CLUSTER:-customer360}"
KCTX="kind-${CLUSTER}"
kubectl --context "$KCTX" apply -k "$K8S/overlays/local"
kubectl --context "$KCTX" -n customer360 rollout status deploy/api --timeout=300s || true
kubectl --context "$KCTX" -n customer360 get pods
```
`kind`/`docker`/`kind load` commands are safe (they target by `--name`); only `kubectl` follows current-context. Also pin `--context` on any **destructive** ad-hoc command (`kubectl delete ns ...`) — never trust current-context for deletes.

**Corollary:** a "GKE change detected" hook / warning firing during what you think is a purely local workflow is a real signal that current-context is remote — do not dismiss it as a false positive.

## Related
[[kind lists a cluster even when its node container is stopped]]
[[Never edit a shell script while it is executing]]

## Related

- [[kind lists a cluster even when its node container is stopped]]
- [[Never edit a shell script while it is executing]]
