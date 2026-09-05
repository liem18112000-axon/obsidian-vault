---
title: "luz-docs-import k8s is a StatefulSet with a 300Gi block-disk temp VCT for upload and /tmp"
created: 2026-08-11
type: observation
status: seedling
source: "session 2026-08-11 luz_docs_import apply-file-store"
tags: [kubernetes, luz-docs-import, statefulset, storage]
---

# luz-docs-import k8s is a StatefulSet with a 300Gi block-disk temp VCT for upload and /tmp

In `luz_kubernetes`, `luz-docs-import` is a **StatefulSet** (`kubernetes/luz-docs-import/k8s.yaml`), name `luz-docs-import` (NOT the same as `luz-docs` / `luz_docs`). Key facts for planning temp-storage changes:

- Temp storage today is a per-pod **`volumeClaimTemplate`** `temp-storage`: `ReadWriteOnce`, `storageClassName: standard` (GCE PD block disk), **300Gi**, mounted at BOTH `/luz_docs_import/upload` (subPath `luz_docs_import/upload`) and `/tmp` (subPath `luz_docs_import/tmp`).
- `/tmp` is load-bearing: resteasy-multipart buffers uploaded parts (zip up to `MAX_POST_SIZE` 2GB) to `java.io.tmpdir=/tmp` before the app reads the stream — that is why /tmp is on the big disk. Any migration off the block disk must preserve adequate local scratch for /tmp.
- HPA (`horizontal-pod-autoscaler.yaml`) pins it at min=max=**2 replicas** (fixed, not really autoscaling). With a StatefulSet RWO VCT each of the 2 pods gets its own disk.
- Deployed in **6 envs** (dev, dev-vn, dev-staging, test, performance, swissdec) — **NOT env-prod** (no overlay dir, not in `env-prod/kustomization.yaml`).
- No `securityContext` today → container runs as **root** (Dockerfile does `USER root` and never switches back).

The base manifest is included via `kubernetes/kustomization.yaml`; overlays only JSON-patch mesh/resources/HPA. Related: [[luz-store Filestore mount pattern: shared RWX PVC + fsGroup 2000 / runAsUser 1000]].

## Related

- [[luz-store Filestore mount pattern: shared RWX PVC + fsGroup 2000 / runAsUser 1000]]
