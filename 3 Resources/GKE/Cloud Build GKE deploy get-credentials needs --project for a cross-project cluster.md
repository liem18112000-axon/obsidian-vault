---
title: "Cloud Build GKE deploy: get-credentials needs --project for a cross-project cluster"
created: 2026-07-03
type: lesson
status: seedling
source: "session 2026-07-03"
tags: [gke, cloud-build, gcloud, deploy, gotcha]
---

# Cloud Build GKE deploy: get-credentials needs --project for a cross-project cluster

When a Cloud Build pipeline deploys to a GKE cluster, `gcloud container clusters get-credentials <cluster> --zone <z>` defaults the project to the BUILD service account's own project. If the cluster lives in a different project, this fails with `404 Not found: projects/<build-project>/zones/<z>/clusters/<cluster>` and "No cluster named ... in <build-project>".

Fix: pass `--project=<cluster-project>` explicitly. In Vinnstack the build SA is in `klara-infra` but the `klara-nonprod` cluster is in project `klara-nonprod`, so deploy-gke.sh needed a `CLUSTER_PROJECT` var (distinct from AR_PROJECT=klara-repo and the build project). The build SA also needs `roles/container.developer` on the cluster project.

## Related
[[GKE pod stuck Init with MountVolume secret not found means a required Secret is missing]]

## Related

- [[GKE pod stuck Init with MountVolume secret not found means a required Secret is missing]]
