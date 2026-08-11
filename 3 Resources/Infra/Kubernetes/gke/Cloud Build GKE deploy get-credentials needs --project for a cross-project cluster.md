---
ai_hash: 5bb4f582909979af
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-03
entities: []
source: session 2026-07-03
status: seedling
tags:
- gke
- cloud-build
- gcloud
- deploy
- gotcha
title: 'Cloud Build GKE deploy: get-credentials needs --project for a cross-project
  cluster'
type: lesson
---

# Cloud Build GKE deploy: get-credentials needs --project for a cross-project cluster

When a Cloud Build pipeline deploys to a GKE cluster, `gcloud container clusters get-credentials <cluster> --zone <z>` defaults the project to the BUILD service account's own project. If the cluster lives in a different project, this fails with `404 Not found: projects/<build-project>/zones/<z>/clusters/<cluster>` and "No cluster named ... in <build-project>".

Fix: pass `--project=<cluster-project>` explicitly. In Vinnstack the build SA is in `klara-infra` but the `klara-nonprod` cluster is in project `klara-nonprod`, so deploy-gke.sh needed a `CLUSTER_PROJECT` var (distinct from AR_PROJECT=klara-repo and the build project). The build SA also needs `roles/container.developer` on the cluster project.

## Related
[[GKE pod stuck Init with MountVolume secret not found means a required Secret is missing]]

## Related

- [[GKE pod stuck Init with MountVolume secret not found means a required Secret is missing]]

%% ai-graph-start %%

**Related notes:**
- [[GKE pod stuck Init with MountVolume secret not found means a required Secret is missing]]
- [[klara-prod is a separate GCP project, not a namespace]]
- [[Klara Cloud Build pushes images to klara-repo Artifact Registry with the SA on the trigger]]
- [[Deploying a stateful single-tenant app to GKE with a Cloud SQL proxy sidecar]]
- [[Vinnstack Cloud Build trigger lives in klara-infra, not klara-nonprod]]

%% ai-graph-end %%