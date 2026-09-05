---
title: "luz-docs-import prod scratch peaks under 1GB — 100Gi tmp-scratch (LUZ-158230) is over-provisioned"
created: 2026-08-13
type: observation
status: seedling
source: "session 2026-08-13"
tags: [luz-docs-import, gke, LUZ-158230, sizing, kubernetes, prod]
---

# luz-docs-import prod scratch peaks under 1GB — 100Gi tmp-scratch (LUZ-158230) is over-provisioned

Cloud Monitoring telemetry over 42 days shows the `luz-docs-import` scratch volume on **klara-prod** peaks at **~0.78 GiB (~800 MB)** used on pod-0 (pod-1 ~0), against a live capacity of ~294 GiB — i.e. **0.27%** utilisation. Sub-gigabyte scratch is the real steady-state.

**Implication for LUZ-158230 (`apply-file-store` branch):** the branch introduces a `tmp-scratch` PVC of `storage: 100Gi` at `/tmp`. That is ~125x the observed prod peak and is unjustified by usage — it is inherited-generous headroom, not a measured requirement. A right-sized target is ~10-20Gi (still >10x peak), keeping some margin because on `pd-standard` IOPS scale with disk size and bulk imports lean on temp I/O. Validate again after the file-store rollout, since re-roling `/tmp` could shift the profile.

**Decision applied (2026-08-13):** set `storage: 16Gi` in `kubernetes/luz-docs-import/k8s.yaml` (>20x the ~0.8Gi peak), with an inline comment citing the Cloud Monitoring peak so the value carries its own justification.

**Drift facts found (repo vs live):**
- Repo `kubernetes/luz-docs-import/k8s.yaml` declares `replicas: 1` and a `tmp-scratch` 100Gi PVC. LIVE prod runs **2 replicas** in namespace `prod` and the scratch volume is still named **`temp-storage`** (the old manifest) — so the `tmp-scratch`/100Gi block is NOT deployed to prod yet; it is pending on this branch.
- Live capacity reads **~294 GiB** on BOTH prod and the performance cluster, despite the manifest saying 100Gi — the deployed volumes were provisioned/expanded well beyond the repo value. Do not trust the repo size as deployed reality.

## Related

- [[Read GKE PVC/volume peak usage from Cloud Monitoring when pod RBAC is denied]]
