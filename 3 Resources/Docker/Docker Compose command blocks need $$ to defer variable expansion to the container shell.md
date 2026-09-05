---
title: "Docker Compose command blocks need $$ to defer variable expansion to the container shell"
created: 2026-08-16
type: gotcha
status: seedling
source: "session 2026-08-16 postgres backup sidecar"
tags: [docker, docker-compose, gotcha, shell]
---

# Docker Compose command blocks need $$ to defer variable expansion to the container shell

Inside a Docker Compose `command:`/`entrypoint:` block, a single `$` triggers **Compose variable interpolation at parse time** — resolved from the host shell/`.env`, **not** the container. So `${FOO}` and `$(date)` in a command get substituted (often to empty) before the container ever runs.

To defer expansion to the **container** shell at runtime, escape the dollar as `$$`: write `$${FOO}` and `$$(date +%H%M)`. Compose collapses `$$` → a literal `$`, which the container `/bin/sh -c` then expands using the service `environment:` vars.

This bit a backup sidecar whose sleep-loop compared `$(date +%H%M)` to `${BACKUP_AT}` (an `environment:` var) — both had to become `$$...` to work. Values that SHOULD be interpolated by Compose from `.env` (e.g. `${DB_PASSWORD:?}` in the `environment:` section) correctly keep the single `$`.
