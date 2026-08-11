---
ai_hash: 3f80e44e2bfcab97
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-15
entities: []
tags:
- gcp
- cloud-run
- terraform
title: Cloud Run v2 service design gotchas
---

# Cloud Run v2 service design gotchas

Reusable facts when defining a `google_cloud_run_v2_service` (Cloud Run gen2):

## Image source
Cloud Run can only pull from **Artifact Registry / GCR — not GHCR**. CI must push the deploy image to an AR Docker repo even if it also mirrors to GHCR for general use.

## CI vs Terraform fighting over the image
CI redeploys update the running image on every push. Put `lifecycle { ignore_changes = [template[0].containers[0].image] }` on the service so `terraform apply` doesn't revert to the placeholder/last-known image. Seed `image` with a public placeholder (e.g. `us-docker.pkg.dev/cloudrun/container/hello`) for first create.

## Filesystem
The container filesystem is **read-only except `/tmp`** (in-memory, counts against memory limit) unless you mount a volume. Per-instance SQLite must live in `/tmp` and is **ephemeral** — gone on instance recycle and not shared across instances, so cap `max_instance_count = 1` until you move to a real DB.

## Reaching private VPC resources (e.g. Memorystore Redis)
Use **Direct VPC egress** — no Serverless VPC Access connector needed in gen2:
```hcl
vpc_access {
  network_interfaces { network = ...; subnetwork = ... }
  egress = "PRIVATE_RANGES_ONLY"
}
```

## Secrets
Inject Secret Manager values via `env { value_source { secret_key_ref { secret = <secret_id>; version = "latest" } } }`. The runtime SA needs `roles/secretmanager.secretAccessor` per secret. Use a dedicated least-privilege runtime service_account, not the default compute SA.

## Public access
A gen2 service is private by default; grant `roles/run.invoker` to `allUsers` (separate `google_cloud_run_v2_service_iam_member`) only when you want it open. `ingress` controls network reachability; invoker IAM controls auth — both matter.

## Related
- [[Terraform sensitive values cannot key for_each — wrap predicate in nonsensitive()]] — bit when wiring the optional secret env vars on this service.

%% ai-graph-start %%

**Related notes:**
- [[Cloud Run can only pull images from Artifact Registry or GCR, not GHCR]]
- [[Memorystore Redis is always VPC-internal — no public endpoint]]
- [[IAM roles a CI service account needs to build and deploy to Cloud Run]]
- [[Cloud Build repo connection blocked drive build+deploy from GitHub Actions instead]]
- [[Pipe a GCP service-account key straight into a GitHub secret without leaking it]]

%% ai-graph-end %%