---
title: "Refactor Terraform resources into a module as a no-op via terraform state mv"
created: 2026-08-31
type: howto
status: seedling
source: "session 2026-08-31 test-agent tf unify"
tags: [terraform, refactoring, iac, state-mv, module]
---

# Refactor Terraform resources into a module as a no-op via terraform state mv

To DRY up repeated Terraform resources into a reusable module **without destroying/recreating live infrastructure**, move each existing resource in state to its new module address, then prove the refactor is inert:

1. Back up the state file first.
2. Write the module + the module calls; `terraform init` to register it, `terraform validate`.
3. For every resource, `terraform state mv '<old.addr>' 'module.<name>.<addr>'`. A resource with no `count` (addr `foo.bar`) moving into a module whose resource uses `count` becomes `module.x.foo.bar[0]` — the index is part of the new address.
4. `terraform plan` MUST report **"No changes"**. Any create/replace means an address wasn't moved or the module renders different config; any in-place update means an attribute mismatch to chase (see [[Cloud Run v2 env blocks are order-sensitive in Terraform]]).

No `apply` is needed for the refactor — moving blocks between files or into a module changes only addresses (and state), never the real resources. Only the module-nesting changes addresses; relocating a plain resource block between .tf files does not. Worked on the test-agent deployments: 4 Cloud Run services collapsed into one module, plan clean.

## Related

- [[Cloud Run v2 env blocks are order-sensitive in Terraform]]
