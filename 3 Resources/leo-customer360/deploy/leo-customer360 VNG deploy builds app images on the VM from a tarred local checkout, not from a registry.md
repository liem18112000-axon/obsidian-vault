---
title: "leo-customer360 VNG deploy builds app images on the VM from a tarred local checkout, not from a registry"
created: 2026-08-20
type: observation
status: seedling
source: "session 2026-08-20"
tags: [leo-customer360, vng-cloud, deployment, docker, terraform, ci-cd]
---

# leo-customer360 VNG deploy builds app images on the VM from a tarred local checkout, not from a registry

In `leo-customer360`, the VNG Cloud (vServer) deployment does **not** consume a pre-built Docker image from any registry for the application services. Each per-module deploy script (`deployments/server/deploy-api.sh`, `deploy-backend.sh`, `deployments/ads-server/deploy-ads.sh`, `deployments/frontend/deploy-frontend.sh`, `deployments/server/seed_data.sh`) follows the same pattern:

1. `tar -C "$REPO_ROOT" -czf - <service> | ssh $BASTION tar -x -C /opt/c360` — tars the service dir **from the local git working copy** and streams it over SSH to the VM.
2. `apt-get install docker.io` on the VM, then `sudo docker build -t <name> /opt/c360/<service>` — the image is **built on the vServer itself** from that copied source.
3. `docker run -d --network host --env-file ... <name>`.

(The scripts even `sed` out the Dockerfile `RUN --mount=type=cache` BuildKit line because `docker.io` ships no buildx — classic builder only.)

Only **third-party base images** are pulled from public registries on the VM: postgres, keycloak, redis, prometheus/grafana/oauth2-proxy (monitoring), caddy (proxy). Infra itself is Terraform via the `vngcloud/vngcloud` provider.

**GitHub side:** the only workflow is `.github/workflows/ci.yml`, which runs unit tests (`run_all_tests.sh`). There is **no** `docker build`/`docker push`/registry-publish step, so GitHub produces no image artifact today.

**Consequence:** "deploy the image GitHub built" is not wired up. To enable it you must (a) add a GH Actions workflow that builds + pushes each service image to a registry the vServer can reach — GHCR `ghcr.io`, VNG Cloud Container Registry (vCR, keeps pulls in-region), or Docker Hub — built for `linux/amd64` (VMs are Ubuntu 24.04 x64), and (b) replace the tar-source + `docker build` block in each deploy script with `docker login` + `docker pull <registry>/<image>:<tag>` before the existing `docker run`.

## Related

- [[leo-customer360 CI]]
- [[New .sh CI runners must be git-staged with --chmod=+x on Windows or the [ -x ] gate skips them]]
