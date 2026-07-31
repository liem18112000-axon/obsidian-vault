---
ai_hash: d3b32dd6af4d4621
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
aliases:
- luz-kubeneet repo layout
created: 2026-06-10
entities:
- luz_kubernetes
- overlay layout
- system.properties
- C:\Users\dvtliem\Kepler\luz_kubernetes
- kubernetes-overlays
- KEY=VALUE files
- env-dev
- luz-docs
- dev
- dev-vn
- dev-staging
- performance
- test
- swissdec
- prod
- mongodb clusters
- secmail
- devgcp
- luz_docs
- repos
- underscores
- overlay service dirs
- dashes
- luz-kubernetes-add-env skill
- env properties
- overlay environments
- luz-kubeneet
source: session 2026-06-10
status: seedling
tags:
- kepler
- luz
- kubernetes
- kustomize
- config
title: 'luz_kubernetes overlay layout: system.properties per env-<env>/<service>'
type: concept
---

# luz_kubernetes overlay layout: system.properties per env-<env>/<service>

The luz_kubernetes repo (often typed "luz-kubeneet") lives at `C:\Users\dvtliem\Kepler\luz_kubernetes`. Per-environment service configuration is plain `KEY=VALUE` files at `kubernetes-overlays/env-<env>/<service>/system.properties` — e.g. `kubernetes-overlays/env-dev/luz-docs/system.properties`.

The 7 main environments: **dev, dev-vn, dev-staging, performance, test, swissdec, prod** (overlay dirs are `env-dev`, `env-dev-vn`, …). There are extra overlays too (mongodb clusters, secmail, devgcp, …) but those 7 are the deploy targets for service env vars.

Naming convention gotcha: **repos use underscores** (`luz_docs`, `luz_kubernetes`) while **overlay service dirs use dashes** (`luz-docs`). Map accordingly when scripting.

Adding a var to one env only is a classic miss — use [[luz-kubernetes-add-env skill propagates env properties across overlay environments]] to hit all 7.

## Related

- [[luz-kubernetes-add-env skill propagates env properties across overlay environments]]

%% ai-graph-start %%

**Related notes:**
- [[luz-kubernetes-add-env skill propagates env properties across overlay environments]]
- [[Luz plugin repos how skills and hooks are packaged for distribution]]
- [[luz-env-config-reminder hook nudges overlay propagation for new env reads in luz repos]]
- [[luz-skills-plugin packages skills by category directory listed in plugin.json]]
- [[How luz_docs_integration_test repo location is resolved on disk]]

**Relations:**
- luz_kubernetes — *has* — overlay layout
- overlay layout — *uses* — system.properties
- luz_kubernetes — *lives at* — C:\Users\dvtliem\Kepler\luz_kubernetes
- system.properties — *are* — KEY=VALUE files
- system.properties — *located in* — kubernetes-overlays/env-<env>/<service>/system.properties
- kubernetes-overlays/env-dev/luz-docs/system.properties — *is an example of* — system.properties
- dev — *is an environment* — env-dev
- dev-vn — *is an environment* — env-dev-vn
- dev-staging — *is an environment* — env-dev-staging
- performance — *is an environment* — env-performance
- test — *is an environment* — env-test
- swissdec — *is an environment* — env-swissdec
- prod — *is an environment* — env-prod
- dev — *is a deploy target for* — service env vars
- dev-vn — *is a deploy target for* — service env vars
- dev-staging — *is a deploy target for* — service env vars
- performance — *is a deploy target for* — service env vars
- test — *is a deploy target for* — service env vars
- swissdec — *is a deploy target for* — service env vars
- prod — *is a deploy target for* — service env vars
- mongodb clusters — *are* — extra overlays
- secmail — *is an* — extra overlay
- devgcp — *is an* — extra overlay
- luz_docs — *is a type of* — repos
- luz_kubernetes — *is a type of* — repos
- repos — *use* — underscores
- overlay service dirs — *use* — dashes
- luz_docs — *maps to* — luz-docs
- luz_kubernetes — *is often typed* — luz-kubeneet
- luz-kubernetes-add-env skill — *propagates* — env properties
- env properties — *across* — overlay environments
- luz-kubernetes-add-env skill — *is related to* — luz_kubernetes

%% ai-graph-end %%