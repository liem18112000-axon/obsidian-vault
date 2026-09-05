---
title: "GreenNode vDB public_access is non-functional here; reach a private DB only via an in-VPC bastion (native login, not user_data)"
created: 2026-08-18
type: lesson
status: seedling
source: "session 2026-08-18 leo-customer360"
tags: [vngcloud, vdb, vserver, networking, bastion, gotcha]
---

# GreenNode vDB public_access is non-functional here; reach a private DB only via an in-VPC bastion (native login, not user_data)

Two hard-won platform findings for GreenNode/VNG Cloud on the leo-customer360 account (single-AZ HCM03-1C, isolated private subnet):

## 1. vDB `public_access = true` is NON-FUNCTIONAL here
Setting `public_access=true` on `vngcloud_vdb_relational_database` applies cleanly (Terraform: `false -> true`, Modifications complete), but the BACKEND never enables it: the vDB detail API (`GET {vdb_base_url}/vdb-relational/v1/database-instances/id/{id}`) keeps `publicAccess=false`, `publicRwIp=null`, `domainName=null`, `ip=[<private 10.x>]` — polled 60s, never changes. So there is NO public endpoint to connect to; the DB stays private-only. (The provider only exposes `ip` = the private RW ip anyway; the public one would be the API`s `publicRwIp`, but it never populates.) Likely cause: the VPC/subnet has no public gateway, same class of limitation as the disabled AZs. Consequence: you CANNOT reach the DB from a laptop directly — only from inside the VPC (a bastion).

## 2. A vServer user_data cloud-config left sshd DOWN; use native login instead
Booting the bastion with a `user_data` `#cloud-config` (to create the login user + install psql) resulted in a box where **sshd never listens** — after opening tcp/22, SSH to the floating IP returns **Connection REFUSED** (fast RST, NOT timeout), which proves the floating IP DOES route to the box; the box just has nothing on 22. VNG`s DEFAULT cloud-init (used when you set the native `ssh_key`/`user_name`/`user_password` and NO user_data) is what brings sshd up and injects the key. So: prefer native login; do package installs (e.g. postgresql-client) AFTER first SSH (or have your runner apt-install on the bastion). refused-vs-timeout is the key diagnostic: refused = routes+no-listener (fixable on the box); timeout = no route (network/secgroup).

## Related
[[VNG Default secgroup opens nothing inbound; SSH times out until you add a tcp/22 secgrouprule]]

## Related

- [[VNG Default secgroup opens nothing inbound; SSH times out until you add a tcp/22 secgrouprule]]
