---
title: "GKE Immediate-binding StorageClass deadlocks a single-replica StatefulSet across zones"
created: 2026-07-03
type: lesson
status: seedling
source: "Vinnstack GKE deploy 2026-07-03"
tags: [kubernetes, gke, storage, statefulset, gotcha]
---

# GKE Immediate-binding StorageClass deadlocks a single-replica StatefulSet across zones

A single-replica StatefulSet whose `volumeClaimTemplates` omit a `storageClassName` inherits the cluster default. On GKE that default (`standard`, provisioner `kubernetes.io/gce-pd`) uses `volumeBindingMode: Immediate` — the PV is provisioned in an arbitrary zone *before* the pod is scheduled. If the only schedulable (untainted) nodes live in a different zone than the disk, the pod is stuck `Pending` forever, since a zonal PD can only attach to nodes in its own zone.

**Symptom events:** `pod has unbound immediate PersistentVolumeClaims`, then `node(s) didn't match PersistentVolume's node affinity` and `untolerated taint(s)`. Confusingly the PVC shows `Bound` — the deadlock is the PV *zone* vs. the pod's schedulable *nodes*, not an unbound claim.

**Fix:** pin a `WaitForFirstConsumer` StorageClass on the volume claim template — on GKE `standard-rwo` (provisioner `pd.csi.storage.gke.io`). Then the scheduler picks the node first and the disk is provisioned in that node's zone. Because `volumeClaimTemplates` are immutable, you must delete + recreate the StatefulSet and its PVC (safe only when the volume holds no data yet).

Diagnose with: `kubectl get pv <pv> -o jsonpath="{.spec.nodeAffinity}"` (disk zone) vs. `kubectl get nodes -L topology.kubernetes.io/zone` filtered to untainted nodes.

## Related

- [[kubectl rollout status timeout must cover cold image pull time]]
