---
title: "VNG vServer apply-time gotchas: password policy (* @ !) and AZ-restricted volume types (1C needs NVME)"
created: 2026-08-18
type: lesson
status: seedling
source: "session 2026-08-18 leo-customer360 deployments/server"
tags: [vngcloud, vserver, password, volume-type, availability-zone, gotcha]
---

# VNG vServer apply-time gotchas: password policy (* @ !) and AZ-restricted volume types (1C needs NVME)

More GreenNode/VNG Cloud vServer apply-time validations (none caught by terraform plan) hit while creating `vngcloud_vserver_server`:

## Password policy
`user_password` must contain **>=1 lowercase, >=1 uppercase, >=1 digit, and one of `* @ !`** — ONLY those three specials satisfy the special requirement (a password with `_ % +` fails). Error: `Status Code: 400, {"message":"The value for the password must contains: least 1 lowercase characters, least 1 uppercase character, least 1 number and (* or @ or !)"}`. Generate with e.g. an `@` guaranteed. Terraform RE2 has no lookahead, so validate with four separate `can(regex("[a-z]"...))` / `[A-Z]` / `[0-9]` / `[@!*]` checks.

## Volume type is AZ-restricted, but the catalog doesnt show which AZ
Error: `{"message":"This volume type dont support zone with ID :HCM03-1C"}`. The volume-type JSON `zoneId` field is just the volume-TYPE-ZONE id (SSD/NVME grouping), NOT the AZ — so you cant tell from the catalog which AZ a volume type supports; the create API enforces it. For THIS account, **HCM03-1C requires NVME** volume types; the SSD group is rejected in 1C. Only two volume-type zones exist (SSD id 1B9FF75D, NVME id 63D9E33A); each type is named by IOPS ("3000" base). Fix: set volume_type_zone_name = "NVME".

## Combining cloud-init with SSH login
VNG forbids user_data together with user_name/user_password/ssh_key. To get BOTH an SSH login and a bootstrap script, put everything in user_data (a cloud-config `users:` block with the RSA key + `packages:`/`runcmd:`) and leave the native args empty.

## Related
[[VNG vServer user_data is mutually exclusive with username/password/ssh_key]]
[[VNG vServer flavor zones are per-AZ under one shared name; the flavor s flavorZoneId field IS the AZ]]

## Related

- [[VNG vServer user_data is mutually exclusive with username/password/ssh_key]]
