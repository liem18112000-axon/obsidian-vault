---
title: "Gate Terraform apply to create-if-absent except on the release branch"
created: 2026-06-14
type: howto
status: seedling
source: "accesstrade provision.yml, 2026-06-14"
tags: [terraform, github-actions, ci-cd, gcp, idempotency]
---

# Gate Terraform apply to create-if-absent except on the release branch

To stop a CI pipeline from churning live infrastructure on every push while still reconciling it for releases, gate the `terraform apply` step on **existence OR release branch**:

```yaml
if: >-
  steps.detect.outputs.exists == 'false' ||
  github.ref_name == 'release' ||
  (github.event_name == 'workflow_dispatch' && inputs.force_apply)
```

Semantics: on ordinary branches apply runs **only the first time** (when the deployment doesn't exist yet — a create-if-absent); on the `release` branch it **always** applies so infra changes ship with a release; a manual `workflow_dispatch` input can force it.

Key decision: detect "does it exist yet?" against the **real world**, not Terraform state — e.g. `gcloud sql instances describe <name> >/dev/null 2>&1` setting `exists=true/false` into `$GITHUB_OUTPUT`. Real-world detection behaves correctly on a clean CI runner even if remote state isn't configured, whereas a `terraform state list` check silently reads "not provisioned" whenever local state is absent and would re-apply every run.

Supporting bits: `setup-terraform` with `terraform_wrapper: false` so the detect step sees raw exit codes; keep project/region/name in **one** place (job-level `TF_VAR_*` env, also reused to build the lookup name) so the gate's name never drifts from what Terraform creates; add a `concurrency` group to prevent two applies at once.

From the accesstrade_integration `.github/workflows/provision.yml` (Cloud SQL + Memorystore).

## Related
[[Memorystore Redis is always VPC-internal — no public endpoint]]
[[Cloud SQL ssl_mode replaced the require_ssl boolean in the hashicorp google provider]]

## Related

- [[Memorystore Redis is always VPC-internal — no public endpoint]]
- [[Cloud SQL ssl_mode replaced the require_ssl boolean in the hashicorp google provider]]
