---
title: "Build and roll out luz-jsonstore to dev (Cloud Build trigger + Deployment rollout)"
created: 2026-08-25
type: howto
status: seedling
source: "session 2026-08-25"
tags: [luz-jsonstore, cloud-build, gke, rollout, dev]
---

# Build and roll out luz-jsonstore to dev (Cloud Build trigger + Deployment rollout)

luz-jsonstore ships to dev in TWO steps; the build does NOT deploy.

1. Build: run the Cloud Build trigger `jsonstore-service` (project klara-infra, region europe-west6, buildfile cloudbuild.yaml):
   gcloud builds triggers run jsonstore-service --project=klara-infra --region=europe-west6 --branch=<branch>   (or --sha=<sha>)
   It builds + pushes an image tagged with the FULL git commit SHA to:
   europe-west6-docker.pkg.dev/klara-repo/artifact-registry-container-images/luz-jsonstore:<sha>
   cloudbuild.yaml has no kubectl/rollout step, so nothing deploys yet.
   Poll: gcloud builds describe <build-id> --project=klara-infra --region=europe-west6 --format='value(status)' until SUCCESS.

2. Rollout: the dev workload is a DEPLOYMENT named luz-jsonstore (container luz-jsonstore) in namespace dev - NOT a StatefulSet. So the StatefulSet-oriented skills (google-skill-rollout-latest, luz-skill-ship) do not fit the rollout. Use:
   kubectl set image deployment/luz-jsonstore luz-jsonstore=<registry>/luz-jsonstore:<sha> -n dev
   kubectl rollout status deployment/luz-jsonstore -n dev
   (If the tag is reused, kubectl rollout restart deployment/luz-jsonstore -n dev re-pulls instead.)

The dev app is reached through the api-forwarder gateway: http://<forward>/luz_jsonstore/api/... (kubectl port-forward -n dev services/api-forwarder 8080:8080).

## Related

- [[Run local luz-jsonstore against a real tenant GKE Mongo via port-forwards]]
