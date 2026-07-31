---
title: "LEO CDP CI provisions deps CI-natively, pinned to devops-script versions for parity"
created: 2026-06-06
type: lesson
status: seedling
source: "leo-cdp-framework ci-cd.yml work 2026-06-06"
tags: [leo-cdp, ci, github-actions, devops, decision]
---

# LEO CDP CI provisions deps CI-natively, pinned to devops-script versions for parity

The CI `validate` job in `.github/workflows/ci-cd.yml` provisions its dependencies and JDK **CI-natively** — GitHub `services:` containers + the `actions/setup-java` action — rather than invoking the VM-oriented `devops-script` provisioning scripts. (An earlier attempt to call `devops-script/docker-arangodb/start.sh` + `install-java.sh` was reverted as the wrong tradeoff for an ephemeral runner.)

**Why CI-native wins here:** `services:` containers are auto health-gated and torn down; `setup-java` is cached and deterministic. Calling the VM scripts instead forced a `docker-compose` v1→v2 shim, a manual readiness wait loop, and a `JAVA_HOME` pin hack — fragility for little gain. The devops scripts are designed for long-lived hosts (`/build/cdp-instance`, `nohup`, persistent volumes, `restart: unless-stopped`), not ephemeral CI.

**Keeping the legitimate kernel of "use devops-script" — production parity — without the fragility:** pin the `services:` images to the SAME versions the `devops-script/docker-arangodb` compose declares (`arangodb:3.11.14`, `redis:7.4`). Parity is the real goal; version-pinning achieves it. `devops-script` remains the source of truth for *deployment*, which is its actual purpose.

**General principle:** "single source of truth" for *deployment* scripts should not be force-fit onto *CI* provisioning — instead share the load-bearing facts (versions, ports, config) and let each environment use its idiomatic mechanism.

## Related
[[GitHub Actions runners pick JDK from inherited JAVA_HOME, not PATH]]
[[Shim legacy docker-compose v1 to docker compose v2 on GitHub runners]]

## Related

- [[3 Resources/Infra/CI-CD/GitHub Actions/GitHub Actions runners pick JDK from inherited JAVA_HOME, not PATH]]
- [[Shim legacy docker-compose v1 to docker compose v2 on GitHub runners]]
