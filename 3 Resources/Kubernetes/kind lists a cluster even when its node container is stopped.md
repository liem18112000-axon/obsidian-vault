---
title: "kind lists a cluster even when its node container is stopped"
created: 2026-08-07
type: lesson
status: seedling
source: "session 2026-08-07 leo-customer360 up.sh"
tags: [kind, kubernetes, docker, bash, local-dev, gotcha]
---

# kind lists a cluster even when its node container is stopped

`kind get clusters` lists a cluster by name as long as it is **registered**, regardless of whether its `<cluster>-control-plane` Docker container is actually running. After a host or Docker/Rancher-Desktop restart, that container can be `Exited (137)` (137 = 128+9 = SIGKILL, typically OOM or a forced daemon shutdown) while kind still reports the cluster as existing.

**Consequence:** a bring-up script that gates only on `kind get clusters | grep -qx "$CLUSTER"` will treat the dead cluster as healthy, skip recreation, and proceed straight to `kind load` — which then fails with:

```
ERROR: failed to detect containerd snapshotter: command "docker exec ... containerd config dump" failed ...
Command Output: Error response from daemon: container ... is not running
```

**Recovery / self-heal guard** — put this right after the "already exists" branch so the script survives reboots:

```bash
NODE="${CLUSTER}-control-plane"
docker start "$NODE" >/dev/null 2>&1 || true   # no-op if already running
kubectl --context "kind-${CLUSTER}" wait --for=condition=Ready \
  "node/${NODE}" --timeout=120s || true
```

`docker start` on a running container is a harmless successful no-op; the `kubectl wait` gives the kubelet/containerd a moment to come back before the subsequent `kind load` / `kubectl apply`.

## Related
[[Never edit a shell script while it is executing]]

## Related

- [[Never edit a shell script while it is executing]]
