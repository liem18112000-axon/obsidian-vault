---
title: "CRLF in a tfvars user_data heredoc makes Terraform force-replace VNG vServers"
created: 2026-08-22
type: lesson
status: seedling
source: "session 2026-08-22"
tags: [leo-customer360, terraform, vngcloud, crlf, drift, gotcha, user_data]
---

# CRLF in a tfvars user_data heredoc makes Terraform force-replace VNG vServers

**Dangerous latent drift** found by `./deploy-all.sh uat plan` on the leo-customer360 `server` module.

## Symptom
`terraform plan` on the server module shows `2 to add, 2 to destroy` — both vServers (`this["api"]`, `this["1x2"]`) **must be replaced** because `~ user_data ... # forces replacement`. But in the diff, **every `-` (old) line equals its `+` (new) line byte-for-byte** — the content is identical.

## Cause
`deployments/server/overlays/uat.tfvars` (which holds the `user_data` cloud-init heredoc) has **CRLF** line terminators on the Windows checkout, while the resource's stored `user_data` in Terraform state has **LF**. Terraform compares the two strings, sees \r\n vs \n on every line -> treats `user_data` as changed. The vngcloud provider marks `user_data` as force-new, so it plans to **destroy + recreate both boxes** — which would wipe every running container (api, redis, keycloak, jaeger, …), volumes, and possibly reassign floating IPs — all for a no-op.

## Do NOT apply
Never run `server/deploy.sh uat apply` (or `deploy-all.sh uat apply` without `--skip server`) while this diff stands.

## Fixes (pick one)
1. **Best / durable:** add `lifecycle { ignore_changes = [user_data] }` to `vngcloud_vserver_server.this` in the server module. user_data only matters at FIRST boot; ignoring later drift stops spurious replacements permanently (survives future CRLF churn).
2. Normalize the overlay to **LF** (git `.gitattributes`: `*.tfvars text eol=lf`, then renormalize) so it matches state — but Windows editors can reintroduce CRLF.
3. Operationally: always `deploy-all.sh uat apply --skip server` (server is rarely re-applied).

General lesson: on Windows, any Terraform string attribute that is **force-new** (user_data, cloud-init, scripts) is a replacement landmine if its source file is CRLF and state is LF. Pin `eol=lf` for such files and/or `ignore_changes`.

## Related
[[Configure vStorage S3 backend creds in each component .env so deploy scripts self-auth]]

## Related

- [[Configure vStorage S3 backend creds in each component .env so deploy scripts self-auth]]
