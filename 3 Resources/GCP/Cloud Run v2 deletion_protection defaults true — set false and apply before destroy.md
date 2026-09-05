---
title: "Cloud Run v2 deletion_protection defaults true — set false and apply before destroy"
created: 2026-08-27
type: lesson
status: seedling
source: "session 2026-08-27 — kga deployments"
tags: [cloud-run, terraform, gcp, gotcha, deletion-protection]
---

# Cloud Run v2 deletion_protection defaults true — set false and apply before destroy

Terraform `google_cloud_run_v2_service` has `deletion_protection` that **defaults to `true`** (google provider v6+). Any destroy or replace fails with: `cannot destroy service without setting deletion_protection=false and running terraform apply`.

**Fix is TWO steps — you cannot flip the flag and destroy in the same apply:**
1. Add `deletion_protection = false` to the resource, then `terraform apply` (updates the live service in place; often `-target=google_cloud_run_v2_service.<name>` to isolate it).
2. THEN run the destroy/replace (`terraform destroy`, or the apply that forces replacement).

Same trap exists on other GCP resources with delete protection (e.g. `google_sql_database_instance` `deletion_protection`, BigQuery datasets). For dev, set it false from the start; for prod keep it true (or a variable) to guard against accidental deletion. A plain `image` change is an in-place update and does NOT trigger this — a destroy/replace does (immutable field change like name/location, or an explicit `terraform destroy`).

## Related

- [[Cloud Run v2 has startup_probe + liveness_probe]]
- [[no readiness probe]]
