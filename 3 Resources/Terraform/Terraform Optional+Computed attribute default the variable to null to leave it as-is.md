---
title: "Terraform Optional+Computed attribute: default the variable to null to leave it as-is"
created: 2026-08-16
type: lesson
status: seedling
source: "session 2026-08-16 prod backup wiring"
tags: [terraform, iac, gotcha, vngcloud]
---

# Terraform Optional+Computed attribute: default the variable to null to leave it as-is

When a Terraform provider marks an argument **Optional + Computed** (e.g. `backup_policy_id` / `backup_location_id` on `vngcloud_vdb_postgresql_cluster`), the provider fills in a value if you omit it, and does not reconcile drift afterward.

To wire such an argument through module layers cleanly, default the pass-through variable to **`null`**, not `""`. Passing `null` makes Terraform treat the argument as *unset*, so the provider keeps whatever value it computed / the console assigned. Passing an **empty string** is a real value and can attempt to CLEAR the attribute, producing spurious diffs or unintended changes.

This is also how you conditionally omit a **top-level** argument: `dynamic` blocks only work for nested blocks, so for a scalar arg you set it to a variable that is `null` when you want it absent. Pattern: `variable "x" { type = string; default = null }` then `x = var.x` in the resource.

Used to thread VNG Backup Center IDs into the LEO CDP prod PG cluster — see [[VNG vDB PostgreSQL cluster has no Terraform backup args (standalone-only)]].

## Related

- [[VNG vDB PostgreSQL cluster has no Terraform backup args (standalone-only)]]
