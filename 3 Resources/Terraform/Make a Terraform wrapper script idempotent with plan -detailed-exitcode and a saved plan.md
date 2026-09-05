---
title: "Make a Terraform wrapper script idempotent with plan -detailed-exitcode and a saved plan"
created: 2026-08-17
type: howto
status: seedling
source: "session 2026-08-17"
tags: [terraform, idempotency, bash, iac, devops]
---

# Make a Terraform wrapper script idempotent with plan -detailed-exitcode and a saved plan

`terraform apply` is already declarative/idempotent, but a wrapper script can make re-runs cleaner and safer:

**Plan-gate with `-detailed-exitcode`:** `terraform plan -input=false -detailed-exitcode -out=tfplan` returns **0 = no changes, 2 = changes present, 1 = error**. Branch on it: on 0, print "already up to date" and skip apply (true no-op); on 2, run `terraform apply -input=false tfplan` (apply the SAVED plan, so what runs is exactly what was reviewed — no TOCTOU drift between plan and apply); on 1, exit with the error.

**Gotcha:** `-detailed-exitcode`'s exit 2 trips `set -e`. Wrap just that plan call in `set +e` / capture `$?` / `set -e`, else the script dies on the very 'changes exist' case you want to handle.

**Also:** `-input=false` everywhere (never hang waiting for prompts in automation); `-lock-timeout=120s` (wait for a held state lock instead of failing a concurrent re-run); `cd "$(dirname "$0")"` (run from the script's dir regardless of caller cwd); gitignore the `tfplan` artifact.

**The real idempotency source is Terraform STATE, not the script.** With only local state, a second run from another machine/CI has no record of the resource and will CREATE A DUPLICATE. For cross-machine idempotency use a shared remote backend with locking (or `terraform import` a pre-existing resource into state first).

See [[Provision GreenNode/VNG Cloud vDB PostgreSQL with the vngcloud Terraform provider]].

## Related

- [[Provision GreenNode/VNG Cloud vDB PostgreSQL with the vngcloud Terraform provider]]
