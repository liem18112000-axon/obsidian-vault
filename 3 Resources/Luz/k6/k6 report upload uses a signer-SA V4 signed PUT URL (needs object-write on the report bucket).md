---
title: "k6 report upload uses a signer-SA V4 signed PUT URL (needs object-write on the report bucket)"
created: 2026-08-18
type: howto
status: seedling
source: "session 2026-08-18 · k6/cloudbuild.yaml"
tags: [k6, gcs, signed-url, iam, cloudbuild, luz-docs-import, gotcha]
---

# k6 report upload uses a signer-SA V4 signed PUT URL (needs object-write on the report bucket)

The luz_docs_import k6 load-test build (`k6/cloudbuild.yaml`, step `run-load-tests`) does NOT mount GCS creds into the k6 pod. Instead the Cloud Build step — running as `cloudbuild-bitbucket-sa@klara-infra.iam.gserviceaccount.com` — generates a **V4-signed PUT URL** via `gcloud storage sign-url --http-verb=PUT --impersonate-service-account=$_SIGNER_SA` (where `_SIGNER_SA` is that same SA, i.e. self-impersonation), then injects it into `gke/k8s.yaml` (`REPLACE_GCS_REPORT_SIGNED_URL`). The k6 pod uploads its HTML report by a plain PUT to that URL — no cluster-side auth setup.

Report object: `gs://${_GKE_ENV}-luz-docs-import-k6-reports/${_GKE_ENV}/$JOB_NAME-$BUILD_ID.html` (unique per build). Bucket region europe-west6 (asia-southeast1 for dev-vn). TTL `_REPORT_URL_TTL=24h`.

**Gotcha (the load-bearing fact):** a V4 signed URL authorizes exactly what the *signing* SA is allowed to do — signing is offline and does NOT check bucket perms, so a signed URL can be minted successfully yet the PUT still 403s at upload time. Therefore the signer SA must itself hold object-write on the target bucket. As of 2026-08-18 only `performance-luz-docs-import-k6-reports` exists (project klara-nonprod); it had zero binding for the SA until I granted `roles/storage.objectAdmin`. `objectCreator` would suffice (names are unique per BUILD_ID) but objectAdmin was chosen for overwrite/cleanup tolerance.

Two separate IAM concerns, do not conflate: (1) BUCKET object-write on the signer SA — this note; (2) `sign-url --impersonate-service-account` needs the *caller* to have `roles/iam.serviceAccountTokenCreator` on `_SIGNER_SA` (trivially satisfied under self-impersonation only if the SA has TokenCreator on itself) — an SA/project-level grant, not a bucket binding.

## Related

- [[Luz individual tenant = bare POST luztenant{username}tenants (no INDIVIDUAL flag)]]
