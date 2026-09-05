---
title: "GreenNode vDB create constraints: instance name 6-20 chars, password start-with-letter, package family s2-general"
created: 2026-08-17
type: lesson
status: seedling
source: "session 2026-08-17"
tags: [greennode, vngcloud, vdb, terraform, validation, gotcha]
---

# GreenNode vDB create constraints: instance name 6-20 chars, password start-with-letter, package family s2-general

Concrete field constraints the GreenNode/VNG Cloud vDB create API (POST database-instances) enforces server-side (400 `in_valid` otherwise) — learned by hitting them via the vngcloud Terraform provider:

- **Instance `name`: 6-20 characters.** `leo-customer360-pg-uat` (22) was rejected 'must be at least 6 and max 20'. Encode as a Terraform `validation { length(var.instance_name) >= 6 && <= 20 }` to fail at plan.
- **Master `password` format:** must **start with a letter** and **must not start or end with a special character** (documented allowed specials ≈ `!#$%&()*+-.:<=>?@[]^_{|}~`, but treat the exact set as UNVERIFIED — single source). A 28-char password with `= * +` was rejected 'must be right format'; a 16-char one starting with a letter, ending with a letter, with only `_` as special, passed. Guard with `can(regex("^[A-Za-z].*[A-Za-z0-9]$", var.db_password))`.
- **Package family is `s2-general`, not `s-general`.** The valid name was `db.s2-general-8x16` (flavorId 3618, 8 vCPU/16 GB, packageId 273). The data source returns an empty id for a wrong name (no error) — see [[vngcloud vDB package/volume data source returns empty id on no-match (guard with a precondition)]].
- **`subnet`/netIds must be a REAL subnet id** in the same zone; the placeholder `sub-xxxx...` returns 'The field subnet is invalid / not_found'.
- The create body also derives `ram`/`vcpus`/`flavorId`/`volumeTypeSku`/`zoneId` from the package + volume + zone lookups.

See [[Provision GreenNode/VNG Cloud vDB PostgreSQL with the vngcloud Terraform provider]].

## Related

- [[Provision GreenNode/VNG Cloud vDB PostgreSQL with the vngcloud Terraform provider]]
- [[vngcloud vDB package/volume data source returns empty id on no-match (guard with a precondition)]]
