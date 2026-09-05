---
title: "Escape shell ${VAR} as $${VAR} in a Terraform Cloud Run command list"
created: 2026-08-28
type: lesson
status: seedling
source: "session 2026-08-28, test_plan_definition M6"
tags: [terraform, cloud-run, gcp, gotcha]
---

# Escape shell ${VAR} as $${VAR} in a Terraform Cloud Run command list

When a `google_cloud_run_v2_service` container overrides `command` with a shell that must expand a runtime env var — e.g. Cloud Run injects `$PORT` — Terraform parses `${PORT}` as its OWN interpolation and fails with `Invalid reference: A reference to a resource type must be followed by at least one attribute access`.

Escape it by doubling the dollar so Terraform emits a literal `${PORT}` for the shell:
```hcl
command = ["sh", "-c", "uvicorn pkg.server:app --host 0.0.0.0 --port $${PORT}"]
```
`$${...}` → literal `${...}`. (The default Dockerfile CMD never hits this because the string isn't in a .tf file; the problem only appears when you inline the command in Terraform.) Verify with `terraform validate`.
