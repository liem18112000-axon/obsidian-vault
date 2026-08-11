---
ai_hash: 96a4a79ace0fc1a0
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-06
entities:
- LEO CDP CI
- dependencies
- CI-native provisioning
- devops-script versions
- parity
- CI `validate` job
- .github/workflows/ci-cd.yml
- JDK
- GitHub `services:` containers
- '`actions/setup-java` action'
- VM-oriented `devops-script` provisioning scripts
- '`devops-script/docker-arangodb/start.sh`'
- '`install-java.sh`'
- ephemeral runner
- '`docker-compose` v1→v2 shim'
- manual readiness wait loop
- '`JAVA_HOME` pin hack'
- devops scripts
- long-lived hosts
- deployment
- '`services:` images'
- '`devops-script/docker-arangodb` compose'
- '`arangodb:3.11.14`'
- '`redis:7.4`'
- version-pinning
- single source of truth
- deployment scripts
- CI provisioning
- load-bearing facts
- GitHub Actions runners pick JDK from inherited JAVA_HOME, not PATH
- Shim legacy docker-compose v1 to docker compose v2 on GitHub runners
source: leo-cdp-framework ci-cd.yml work 2026-06-06
status: seedling
tags:
- leo-cdp
- ci
- github-actions
- devops
- decision
title: LEO CDP CI provisions deps CI-natively, pinned to devops-script versions for
  parity
type: lesson
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

%% ai-graph-start %%

**Related notes:**
- [[Running the LEO CDP GHCR image needs mounted configs (image ships JARs only)]]
- [[GitHub Actions runners pick JDK from inherited JAVA_HOME, not PATH]]
- [[Shim legacy docker-compose v1 to docker compose v2 on GitHub runners]]
- [[LEO CDP SYSTEM_ENV_VARS still requires database-configs.json to exist first]]
- [[Job-level defaults.run.working-directory breaks Initialize containers (pre-checkout)]]

**Relations:**
- LEO CDP CI — *provisions* — dependencies
- LEO CDP CI — *uses* — CI-native provisioning
- dependencies — *pinned to* — devops-script versions
- devops-script versions — *provides* — parity
- CI `validate` job — *defined in* — .github/workflows/ci-cd.yml
- CI `validate` job — *provisions* — dependencies
- CI `validate` job — *provisions* — JDK
- CI `validate` job — *uses* — CI-native provisioning
- CI `validate` job — *avoids* — VM-oriented `devops-script` provisioning scripts
- CI-native provisioning — *involves* — GitHub `services:` containers
- CI-native provisioning — *involves* — `actions/setup-java` action
- VM-oriented `devops-script` provisioning scripts — *includes* — `devops-script/docker-arangodb/start.sh`
- VM-oriented `devops-script` provisioning scripts — *includes* — `install-java.sh`
- GitHub `services:` containers — *characteristic* — auto health-gated
- GitHub `services:` containers — *characteristic* — torn down
- `actions/setup-java` action — *characteristic* — cached
- `actions/setup-java` action — *characteristic* — deterministic
- VM-oriented `devops-script` provisioning scripts — *forced* — `docker-compose` v1→v2 shim
- VM-oriented `devops-script` provisioning scripts — *forced* — manual readiness wait loop
- VM-oriented `devops-script` provisioning scripts — *forced* — `JAVA_HOME` pin hack
- devops scripts — *designed for* — long-lived hosts
- devops scripts — *not designed for* — ephemeral runner
- `services:` images — *pinned to* — `devops-script/docker-arangodb` compose
- `devops-script/docker-arangodb` compose — *declares version* — `arangodb:3.11.14`
- `devops-script/docker-arangodb` compose — *declares version* — `redis:7.4`
- version-pinning — *achieves* — parity
- `devops-script` — *is source of truth for* — deployment
- deployment scripts — *should not be used for* — CI provisioning
- deployment scripts — *share* — load-bearing facts
- CI provisioning — *uses* — load-bearing facts
- LEO CDP CI — *related to* — GitHub Actions runners pick JDK from inherited JAVA_HOME, not PATH
- LEO CDP CI — *related to* — Shim legacy docker-compose v1 to docker compose v2 on GitHub runners

%% ai-graph-end %%