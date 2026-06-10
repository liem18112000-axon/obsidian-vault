---
title: "luz_kubernetes overlay layout: system.properties per env-<env>/<service>"
created: 2026-06-10
aliases: ["luz-kubeneet repo layout"]
type: concept
status: seedling
source: "session 2026-06-10"
tags: [kepler, luz, kubernetes, kustomize, config]
---

# luz_kubernetes overlay layout: system.properties per env-<env>/<service>

The luz_kubernetes repo (often typed "luz-kubeneet") lives at `C:\Users\dvtliem\Kepler\luz_kubernetes`. Per-environment service configuration is plain `KEY=VALUE` files at `kubernetes-overlays/env-<env>/<service>/system.properties` — e.g. `kubernetes-overlays/env-dev/luz-docs/system.properties`.

The 7 main environments: **dev, dev-vn, dev-staging, performance, test, swissdec, prod** (overlay dirs are `env-dev`, `env-dev-vn`, …). There are extra overlays too (mongodb clusters, secmail, devgcp, …) but those 7 are the deploy targets for service env vars.

Naming convention gotcha: **repos use underscores** (`luz_docs`, `luz_kubernetes`) while **overlay service dirs use dashes** (`luz-docs`). Map accordingly when scripting.

Adding a var to one env only is a classic miss — use [[luz-kubernetes-add-env skill propagates env properties across overlay environments]] to hit all 7.

## Related

- [[luz-kubernetes-add-env skill propagates env properties across overlay environments]]
