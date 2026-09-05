---
title: "Single-to-multi container Cloud Run update fails in-place; use terraform -replace"
created: 2026-08-31
type: lesson
status: seedling
source: "session 2026-08-31 sidecar apply"
tags: [terraform, cloud-run, sidecar, gotcha, gcp, replace]
---

# Single-to-multi container Cloud Run update fails in-place; use terraform -replace

Converting an EXISTING single-container `google_cloud_run_v2_service` to multi-container (adding a sidecar) with an in-place `terraform apply` fails at the API:

  Error 400: template.containers: Revision template should contain exactly one container with an exposed port.

even when the config is correct (exactly one container has `ports`). Cause: terraform diffs the container list positionally and mis-assigns the ingress `ports` while morphing the old single container into the new sidecar — the API receives two containers with a port (or zero).

**Fix: force a fresh create instead of an in-place update:**
`terraform apply -replace='module.x.google_cloud_run_v2_service.this[0]'`
The replacement builds the multi-container revision from config with no positional carry-over. The Cloud Run **URL is stable across replace** (derived from service name/project/region), so clients are unaffected — only a brief revision swap.

**Related hard lesson from the same run:** the first (failed) in-place apply had already DESTROYED sibling resources in parallel (the standalone agent services) before erroring on the service updates, leaving the system down. terraform apply is not atomic — a partial failure can destroy some resources while others error. Back up state first and be ready to fix-forward (`-replace`) or roll back. See [[Cloud Run v2 multi-container sidecar in Terraform]].

## Related

- [[Cloud Run v2 multi-container sidecar in Terraform]]
