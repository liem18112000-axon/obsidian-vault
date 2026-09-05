---
title: "Cloud Run :latest does not roll a new revision on terraform apply — deploy by digest"
created: 2026-08-28
type: lesson
status: seedling
source: "session 2026-08-27"
tags: [cloud-run, terraform, deployment, gotcha, docker]
---

# Cloud Run :latest does not roll a new revision on terraform apply — deploy by digest

Deploying a Cloud Run service by the mutable tag `:latest` and then running `terraform apply` (or `gcloud run deploy` with the same ref) does NOT roll a new revision when the *reference string* is unchanged: Terraform diffs the literal `image = "...:latest"`, sees no change, and skips the container — so a freshly pushed `:latest` keeps serving the OLD code. (An unrelated env/annotation change masks this, because it forces a new revision that also pulls the new image — which is why "add an env var" deploys seem to work but "code-only" deploys silently dont.)

Fix: deploy by **immutable digest** so the ref actually changes:
```bash
D=$(gcloud artifacts docker images describe "$IMG:latest" \
      --format="value(image_summary.fully_qualified_digest)")   # or images list --format="value(version)"
terraform apply -var="image=$D"     # $IMG@sha256:...
```
Alternatives: a unique tag per build (git SHA), or bump a template annotation to force a revision. `image_summary.digest` alone returned empty in one gcloud version — `fully_qualified_digest` (repo@sha256:...) or `images list --include-tags --format="value(version)"` is more reliable.

See [[Cloud Run one-port limit forces co-located HTTP servers into separate services]].

## Related

- [[Cloud Run one-port limit forces co-located HTTP servers into separate services]]
