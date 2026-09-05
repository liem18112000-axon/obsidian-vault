---
title: "Terraform optional-resource toggle: create-or-reuse via count + a local that picks the id"
created: 2026-08-17
type: howto
status: seedling
source: "session 2026-08-17"
tags: [terraform, pattern, count, vngcloud, iac]
---

# Terraform optional-resource toggle: create-or-reuse via count + a local that picks the id

Pattern for "create it if it does not exist" in Terraform (which has no native create-if-absent): a **boolean toggle + count + a local that selects the id**.

```hcl
variable "create_network" { type = bool, default = false }
variable "subnet_id"      { type = string, default = "" }   # used when NOT creating

resource "vngcloud_vserver_network" "this" { count = var.create_network ? 1 : 0  ... }
resource "vngcloud_vserver_subnet"  "this" { count = var.create_network ? 1 : 0
  network_id = vngcloud_vserver_network.this[0].id ... }

locals { subnet_id = var.create_network ? vngcloud_vserver_subnet.this[0].id : var.subnet_id }
# consumer:  subnet_id = local.subnet_id
```

Notes: index `[0]` the counted resource; guard with `lifecycle { precondition }` (e.g. require project_id when create_network, or a real subnet_id otherwise) so misconfig fails at plan. This is create-OR-reuse (ownership), NOT true create-if-absent — Terraform owns the resource when the toggle is on; a data source would error if the thing is missing, so a toggle is the clean approach.

Applied to GreenNode vDB: creating a DB needs a real subnet, and `vngcloud_vserver_network`/`_subnet` BOTH require `project_id` (VNG Cloud `pro-...`, the project the credentials use) — unlike the vDB resource which infers project from the token. See [[GreenNode vDB create constraints: instance name 6-20 chars, password start-with-letter, package family s2-general]].

## Related

- [[GreenNode vDB create constraints: instance name 6-20 chars]]
- [[password start-with-letter]]
- [[package family s2-general]]
