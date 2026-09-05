---
title: "Portainer admin-password bootstrap skips local Docker env - pass -H socket"
created: 2026-08-19
type: howto
status: seedling
source: "session 2026-08-19"
tags: [portainer, docker, gotcha, bootstrap, socket]
---

# Portainer non-interactive admin skips the local env — pass -H socket to fix

**Symptom:** you log into Portainer but see **no containers/images/volumes** — the UI has
an admin user but no Docker data.

**Cause:** mounting `-v /var/run/docker.sock:/var/run/docker.sock` alone does NOT make
Portainer connect to the local Docker. The **local environment is created by the setup
wizard's "Get Started" step** — and bootstrapping the admin non-interactively with
`--admin-password-file` (or `--admin-password`) **skips that wizard entirely**, so no
environment is ever created. The mount is necessary but not sufficient.

**Fix:** pass the endpoint flag on the Portainer command so it auto-creates + connects the
local environment at startup (independent of the wizard):

```
docker run -d --name portainer \
  -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data \
  portainer/portainer-ce:lts \
  -H unix:///var/run/docker.sock --admin-password-file /run/portainer_pw
```

(`-H` seeds the local endpoint even against an existing data volume that was initialized
without one.) Alternative one-time fix: in the UI, Environments → Add environment → Docker
Standalone → Socket → `/var/run/docker.sock` → Connect.

Applied in `leo-customer360` `deployments/monitoring/deploy-monitoring.sh`. Related:
[[Portainer non-interactive admin bootstrap via --admin-password-file]]
