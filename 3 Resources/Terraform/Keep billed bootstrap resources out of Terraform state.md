---
title: "Keep billed bootstrap resources out of Terraform state"
created: 2026-08-17
type: lesson
status: seedling
source: "session 2026-08-17"
tags: [terraform, iac, state, idempotency, design-decision]
---

# Keep billed bootstrap resources out of Terraform state

When a resource is a **prerequisite that is billed and holds data** (e.g. a VNG Cloud vStorage project with a paid quota, a cloud account, a DNS zone you don't own the lifecycle of), prefer a separate **idempotent bootstrap script** over putting it in the Terraform module that manages what sits on top.

Why: `terraform destroy` (or a botched `apply`/state drift) must never be able to delete a paid, data-bearing resource. Keeping it out of state removes that whole class of foot-gun. The bootstrap script should be idempotent — GET/list first, reuse if it already exists, only create when absent — so re-running is safe and it can run before every deploy.

The Terraform module then references the bootstrapped resource by a stable id/name it does not own. Example: `deployments/storage/scripts/create-project.sh` creates the vStorage project; the Terraform (via an S3 key scoped to that project) only manages buckets inside it.

Related: [[vStorage project is a paid prerequisite Terraform cannot create]], [[vStorage REST control-plane API: endpoints and vIAM bearer auth]].

## Related

- [[vStorage project is a paid prerequisite Terraform cannot create]]
- [[vStorage REST control-plane API: endpoints and vIAM bearer auth]]
