---
title: "GreenNode VNG Ubuntu 24.04 image: SSH is broken out-of-the-box; the cloud-init recipe to fix it"
created: 2026-08-18
type: howto
status: seedling
source: "session 2026-08-18 leo-customer360 deployments/server"
tags: [vngcloud, vserver, ubuntu, ssh, cloud-init, gotcha]
---

# GreenNode VNG Ubuntu 24.04 image: SSH is broken out-of-the-box; the cloud-init recipe to fix it

A freshly-booted GreenNode/VNG Cloud **Ubuntu 24.04** vServer (image `1_Ubuntu-24.04x64`) is UNREACHABLE by SSH out of the box, for a stack of reasons that all had to be fixed together. Diagnosed via the server console-log API (`GET {vserver_base_url}/v2/{project}/servers/{id}/console-log`) since you cant SSH in to look.

## The four compounding problems
1. **sshd listens on port 234, not 22.** `/etc/ssh/sshd_config` ships `Port 234`. Probing :22 gives REFUSED forever. (refused = routes-but-no-listener; timeout = no route — this distinction was the key diagnostic throughout.)
2. **`ssh-keygen.service` FAILS at boot** ("Read-only file system" — root fs is `ro` for the first ~3s, remounts `rw` at ~2.9s, but the keygen unit already ran). So no host keys -> sshd exits. cloud-inits config stage regenerates host keys later, but nothing restarts sshd.
3. **socket vs service:** the image has both `ssh.service` (standalone) and `ssh.socket`; touching the socket causes an "Address already in use / already active, refusing" bind fight. Pick ONE — disable the socket, run the service.
4. **cloud-init leaves the login user password-LOCKED**, and even with `UsePAM no`, pubkey auth is then REFUSED ("Permission denied (publickey)") despite a correct key, 700/600 perms, and `PubkeyAuthentication yes`. Unlocking the account (set any password) makes pubkey auth work. THIS was the final blocker.

Also: drop-ins under `/etc/ssh/sshd_config.d/` are NOT `Include`d by this image`s sshd_config — append settings to the MAIN `/etc/ssh/sshd_config`. And use the VNG **ssh_key resource** only for RSA (it rejects ed25519 with "Invalid public key") — but if you inject keys via cloud-init `ssh_authorized_keys` instead, ed25519 is fine and preferred.

## The working cloud-init `user_data` recipe (native ssh_key/user args OFF; VNG forbids user_data + those together)
```
#cloud-config
users:
  - default
  - name: leocdp360
    groups: [sudo]
    sudo: ["ALL=(ALL) NOPASSWD:ALL"]
    ssh_authorized_keys: [ "ssh-ed25519 AAAA... ", "ssh-rsa AAAA... " ]
packages: [ postgresql-client ]
runcmd:
  - ssh-keygen -A
  - sed -i "s/^Port 234$/Port 22/" /etc/ssh/sshd_config
  - printf "\nPubkeyAuthentication yes\nPubkeyAcceptedAlgorithms +ssh-rsa,rsa-sha2-256,rsa-sha2-512,ssh-ed25519\n" >> /etc/ssh/sshd_config
  - bash -c "echo leocdp360:$(openssl rand -base64 18) | chpasswd; usermod -U leocdp360"   # UNLOCK — the crucial bit
  - bash -c "chmod 700 /home/leocdp360/.ssh; chmod 600 /home/leocdp360/.ssh/authorized_keys; chown -R leocdp360:leocdp360 /home/leocdp360/.ssh"
  - systemctl disable --now ssh.socket
  - systemctl enable ssh.service
  - systemctl restart ssh.service
```
Then a secgroup rule opens tcp/22, and psql on this box reaches the private vDB. Reproduced clean: `run-sql.sh` SSHes in as leocdp360 (ed25519) and runs the init SQL against the private DB.

## Related
[[VNG vddb public_access is non-functional here; reach a private DB only via an in-VPC bastion (native login, not user_data)]]
