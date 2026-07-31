---
ai_hash: 906f8ea50b096167
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-03
entities: []
source: session 2026-07-03
status: seedling
tags:
- gke
- kubernetes
- cloud-sql
- workload-identity
- cloudbuild
- vinnstack
title: Deploying a stateful single-tenant app to GKE with a Cloud SQL proxy sidecar
type: howto
---

# Deploying a stateful single-tenant app to GKE with a Cloud SQL proxy sidecar

Deploying Vinnstack (a local-first, single-OPERATOR app) to GKE — the shape that works:

- **StatefulSet, replicas: 1**, not a Deployment. The app keeps its Obsidian vault + config on disk and there is one operator, so it needs a stable identity + a persistent RWO volume (volumeClaimTemplates → /data). Never scale out (shared vault/config).
- **HOME=/data** in the image so ~/.agentic-os config AND the CLI logins (~/.claude, gcloud ADC) live on the persistent volume and survive pod restarts — not just the vault (VINNSTACK_DATA_DIR).
- **Postgres via a Cloud SQL Auth Proxy SIDECAR** (image gcr.io/cloud-sql-connectors/cloud-sql-proxy) in the same pod. App DATABASE_URL then points at 127.0.0.1:5432 (in-pod), NOT the local-dev 15432. Proxy authenticates via **Workload Identity** — the KSA is annotated iam.gke.io/gcp-service-account=<GSA> and the GSA has roles/cloudsql.client. No key files.
- **DB password** goes in a k8s Secret created out-of-band (kubectl create secret … --from-literal=url=…), never applied from git. Commit only a secret.example.yaml shape.
- The image bundles the CLIs the agent drives — add **kubectl + google-cloud-cli-gke-gcloud-auth-plugin** alongside gcloud + the claude CLI so the containerized agent works like it does locally.
- The **browser logins** (claude auth login / gcloud auth login) cant be baked in — after first rollout, `kubectl exec -it` into the pod to do them once (they persist on /data), or rely on Workload Identity for gcloud.

cloudbuild.yaml deploy step: test → build → push → `gcloud container clusters get-credentials` then `kubectl apply -f k8s/` + `kubectl set image statefulset/vinnstack vinnstack=<repo>:$COMMIT_SHA` (SHA-pinned = immutable rollout) + `rollout status`. The build SA needs roles/container.developer on the cluster on top of artifactregistry.writer.

## Related

- [[Connecting to the Vinnstack Cloud SQL Postgres (vinnstackdb) via the Auth Proxy]]

%% ai-graph-start %%

**Related notes:**
- [[Connecting to the Vinnstack Cloud SQL Postgres (vinnstackdb) via the Auth Proxy]]
- [[Non-WI GKE Google API auth mount a GSA key at the well-known ADC path]]
- [[Cloud SQL Auth Proxy needs roles-cloudsql.client on the connecting identity or it 403s NOT_AUTHORIZED]]
- [[Creating the GSA a KSA annotation references activates WI routing and can break a pod]]
- [[Cloud Build GKE deploy get-credentials needs --project for a cross-project cluster]]

%% ai-graph-end %%