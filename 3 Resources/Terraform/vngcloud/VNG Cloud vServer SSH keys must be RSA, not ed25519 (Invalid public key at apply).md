---
title: "VNG Cloud vServer SSH keys must be RSA, not ed25519 (Invalid public key at apply)"
created: 2026-08-18
type: lesson
status: seedling
source: "session 2026-08-18 leo-customer360 deployments/server"
tags: [vngcloud, vserver, ssh, rsa, gotcha]
---

# VNG Cloud vServer SSH keys must be RSA, not ed25519 (Invalid public key at apply)

GreenNode/VNG Cloud vServer rejects **ed25519** SSH public keys — creating `vngcloud_vserver_sshkey` with an `ssh-ed25519 ...` key fails at apply with `Status Code: 400, {"message":"Invalid public key"}`. Fix: use an **RSA** key (`ssh-keygen -t rsa -b 4096`), i.e. an `ssh-rsa AAAA...` public key. The provider/plan does not catch this — it only surfaces at apply when the API validates the key. (Same likely applies to other key types like ecdsa; RSA is the safe choice on VNG.)

## Related
[[VNG Cloud vServer Terraform catalog ids resolve via a zone-UUID lookup chain]]

## Related

- [[VNG Cloud vServer Terraform catalog ids resolve via a zone-UUID lookup chain]]
