---
title: "VNG vServer user_data is mutually exclusive with username/password/ssh_key"
created: 2026-08-18
type: lesson
status: seedling
source: "session 2026-08-18 leo-customer360 deployments/server"
tags: [vngcloud, vserver, cloud-init, user-data, gotcha]
---

# VNG vServer user_data is mutually exclusive with username/password/ssh_key

On GreenNode/VNG Cloud vServer, the `vngcloud_vserver_server` `user_data` (cloud-init) field is **mutually exclusive** with `user_name` + `user_password` + `ssh_key`. Supplying user_data alongside any of them fails at apply: `Status Code: 400, {"message":"User data dont allow input username, password and ssh key."}`. Choose ONE path: (a) SSH-key / password login via user_name/user_password/ssh_key and NO user_data, or (b) a cloud-init user_data script that sets up its own users/keys and leave user_name/user_password/ssh_key unset. For a jump host, path (a) is simpler — install extra packages (e.g. postgresql-client) manually after first SSH. The provider/plan does not catch this; it only surfaces at apply.

## Related
[[VNG Cloud vServer SSH keys must be RSA, not ed25519 (Invalid public key at apply)]]

## Related

- [[VNG Cloud vServer SSH keys must be RSA]]
- [[not ed25519 (Invalid public key at apply)]]
