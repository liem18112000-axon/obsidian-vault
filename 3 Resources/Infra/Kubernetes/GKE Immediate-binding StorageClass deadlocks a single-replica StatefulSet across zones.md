---
ai_hash: c99c11edc30b9718
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-03
entities: []
source: Vinnstack GKE deploy 2026-07-03
status: seedling
tags:
- kubernetes
- gke
- storage
- statefulset
- gotcha
title: GKE Immediate-binding StorageClass deadlocks a single-replica StatefulSet across
  zones
type: lesson
---

# GKE Immediate-binding StorageClass deadlocks a single-replica StatefulSet across zones

A single-replica StatefulSet whose `volumeClaimTemplates` omit a `storageClassName` inherits the cluster default. On GKE that default (`standard`, provisioner `kubernetes.io/gce-pd`) uses `volumeBindingMode: Immediate` — the PV is provisioned in an arbitrary zone *before* the pod is scheduled. If the only schedulable (untainted) nodes live in a different zone than the disk, the pod is stuck `Pending` forever, since a zonal PD can only attach to nodes in its own zone.

**Symptom events:** `pod has unbound immediate PersistentVolumeClaims`, then `node(s) didn't match PersistentVolume's node affinity` and `untolerated taint(s)`. Confusingly the PVC shows `Bound` — the deadlock is the PV *zone* vs. the pod's schedulable *nodes*, not an unbound claim.

**Fix:** pin a `WaitForFirstConsumer` StorageClass on the volume claim template — on GKE `standard-rwo` (provisioner `pd.csi.storage.gke.io`). Then the scheduler picks the node first and the disk is provisioned in that node's zone. Because `volumeClaimTemplates` are immutable, you must delete + recreate the StatefulSet and its PVC (safe only when the volume holds no data yet).

Diagnose with: `kubectl get pv <pv> -o jsonpath="{.spec.nodeAffinity}"` (disk zone) vs. `kubectl get nodes -L topology.kubernetes.io/zone` filtered to untainted nodes.

## Related

- [[kubectl rollout status timeout must cover cold image pull time]]

%% ai-graph-start %%

**Related notes:**
- [[kubectl rollout status timeout must cover cold image pull time]]
- [[GKE pod stuck Init with MountVolume secret not found means a required Secret is missing]]
- [[Cloud Build $COMMIT_SHA is the full 40-char git SHA, not the short one]]
- [[Creating the GSA a KSA annotation references activates WI routing and can break a pod]]

%% ai-graph-end %%