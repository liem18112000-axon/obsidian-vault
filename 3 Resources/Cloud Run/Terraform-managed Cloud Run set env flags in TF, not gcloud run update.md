---
title: "Terraform-managed Cloud Run: set env flags in TF, not gcloud run update"
created: 2026-09-03
type: lesson
status: seedling
source: "session 2026-09-03 (enable KGA G2-G5)"
tags: [cloud-run, terraform, gcp, iac, gotcha]
---

# Terraform-managed Cloud Run: set env flags in TF, not gcloud run update

When a Cloud Run service is managed by Terraform, enable/disable runtime flags by changing the **Terraform config**, never with an out-of-band `gcloud run services update --set-env-vars`. Terraform treats the container `env` list as desired state, so the next `terraform apply` (even one that only bumps the image) reverts any env var it does not know about. The durable pattern, mirroring the repos existing `tpd_llm_detail`, is a bool variable that conditionally appends entries inside the `env = concat(...)`:

```hcl
var.kga_self_explore ? [
  { name = "KGA_EXPLORE_LOOP", value = "1" },
  # ...
] : [],
```

then set the bool in `terraform.tfvars`. `terraform plan` should show `1 to change, 0 to destroy` touching only that service, with the env names added in-place — a clean signal that nothing else (image, IAM) moved.

## Related
[[Cloud Run 401 response body distinguishes GFE/IAM rejection from app-level auth]]

## Related

- [[Cloud Run 401 response body distinguishes GFE/IAM rejection from app-level auth]]
