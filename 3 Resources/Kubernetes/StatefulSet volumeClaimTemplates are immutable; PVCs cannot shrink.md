---
title: "StatefulSet volumeClaimTemplates are immutable; PVCs cannot shrink"
created: 2026-08-03
type: lesson
status: seedling
source: "session 2026-08-03"
tags: [kubernetes, statefulset, pvc, kustomize, immutable, gotcha]
---

# StatefulSet volumeClaimTemplates are immutable; PVCs cannot shrink

A StatefulSet's `volumeClaimTemplates` (and most spec fields except `replicas`, `template`, `updateStrategy`, `ordinals`, `persistentVolumeClaimRetentionPolicy`, `minReadySeconds`) are **immutable after creation**. So a Kustomize/kubectl patch that changes PVC storage size inside `volumeClaimTemplates` is rejected — and because the apply is atomic, it takes the rest of your patch (e.g. resource limits added in the same StatefulSet doc) down with it, silently leaving the workload un-updated.

Separately, an existing PVC's `spec.resources.requests.storage` can only be **grown, never shrunk** (`Forbidden: field can not be less than status.capacity`).

Practical rules: keep resource/limit and env patches in the `template` (mutable); do NOT try to resize volumes on an existing StatefulSet — to change volume size, recreate the StatefulSet + PVCs. PVC size is a weak "minimise resources" lever anyway; CPU/memory limits are the real one.

## Related
- [[Right-sizing k8s resource limits on the Customer360 stack]]
