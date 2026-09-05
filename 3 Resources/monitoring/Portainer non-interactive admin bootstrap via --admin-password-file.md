---
title: "Portainer non-interactive admin bootstrap via --admin-password-file"
created: 2026-08-19
type: howto
status: seedling
source: "session 2026-08-19"
tags: [portainer, docker, automation, bootstrap, monitoring]
---

# Portainer non-interactive admin bootstrap via --admin-password-file

Portainer CE locks its first-run setup screen if you don't create the admin user within a
few minutes of the container starting ("setup-timeout lock") — annoying for automated /
SSH deploys where nobody opens the browser in time. Bootstrap the admin non-interactively
instead:

1. Write the **plaintext** password to a host file, mode 0600.
2. Bind-mount it read-only and pass `--admin-password-file`:

```bash
printf '%s' "$PW" | sudo tee /opt/app/portainer_pw >/dev/null; sudo chmod 600 /opt/app/portainer_pw
docker run -d --name portainer --restart unless-stopped \
  -p 9443:9443 -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data \
  -v /opt/app/portainer_pw:/run/portainer_pw:ro \
  portainer/portainer-ce:lts --admin-password-file /run/portainer_pw
```

Gotchas:
- `--admin-password-file` takes **plaintext**; the older `--admin-password` wants a
  **bcrypt hash** (needs htpasswd) — the file form avoids that.
- Password must be **>= 12 chars** or Portainer refuses and the container crash-loops.
- Only consumed on first init (when no admin exists); ignored once the admin is set.

Used in `leo-customer360` `deployments/monitoring/deploy-monitoring.sh` (optional, gated on
`PORTAINER_ADMIN_PASSWORD` in .env). Related:
[[Portainer vs Netdata - both are web UIs, ops vs metrics]]
