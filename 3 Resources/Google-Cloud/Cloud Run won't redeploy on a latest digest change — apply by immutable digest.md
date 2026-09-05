---
title: "Cloud Run won't redeploy on a :latest digest change — apply by immutable digest"
created: 2026-08-28
type: lesson
status: seedling
source: "session 2026-08-28 (KGA refactor deploy)"
tags: [gcp, cloud-run, terraform, artifact-registry, deployment, gotcha]
---

# Cloud Run won't redeploy on a :latest digest change — apply by immutable digest

Cloud Run tracks the exact image reference string on a service revision. If a service already points at a **mutable tag** like `...kga:latest` and you rebuild that tag to new content, neither `gcloud`/Terraform nor Cloud Run creates a new revision — because the config string (`kga:latest`) is byte-identical to what is already deployed. Terraform reports "No changes"; the service keeps serving the OLD image behind the tag.

To force the new image out, deploy by the **immutable digest** instead of the tag:
```
DIGEST=$(gcloud artifacts docker images list REPO/IMG --include-tags \
  --filter="tags=latest" --format="value(version)")   # sha256:...
terraform apply -var="image=REPO/IMG@$DIGEST"          # differs from :latest -> new revision
```
The digest string differs from the stored `:latest`, so Terraform/Cloud Run sees a real change and rolls a new revision. (Note: `gcloud artifacts docker images describe ...:latest` can fail with a `containeranalysis.occurrences.list` permission error — that is the vuln-scan metadata, not the digest; use `images list --format="value(version)"` to read the digest without that permission.)

Surfaced deploying a rebuilt `:latest` to two Cloud Run services that were already on `:latest` — a plain re-apply was a no-op until I switched to the digest.
