---
ai_hash: c3f5f2ac9f47a896
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-10
entities:
- luz-kubernetes-add-env skill
- env properties
- overlay environments
- system.properties
- luz_kubernetes overlay layout system.properties per env-envservice
- add-env.sh
- KEY=VALUE properties
- APPLY=1
- SERVICE=luz-docs
- ENVS=dev,dev-vn,dev-staging,performance,test,swissdec,prod
- master/main branch
- custom branch
- BRANCH=<name>
- ALLOW_COMMIT_ON_BRANCH=1
- PUSH=1
- LUZ_KUBERNETES_ROOT
- locate-repo.sh
- luz-kubernetes-root.config
- Key normalization
- property-style names
- env-var form
- tr 'a-z.-' 'A-Z__'
- git diff
- current HEAD
- luz-env-config-reminder hook nudges overlay propagation for new env reads in luz
  repos
source: session 2026-06-10
status: seedling
tags:
- claude-code
- skill
- luz
- kubernetes
- config
title: luz-kubernetes-add-env skill propagates env properties across overlay environments
type: howto
---

# luz-kubernetes-add-env skill propagates env properties across overlay environments

The `luz-kubernetes-add-env` skill (`~/.claude/skills/luz-kubernetes-add-env/`) adds or updates `KEY=VALUE` properties in `system.properties` across all overlay environments of [[2 Areas/Kepler/luz_kubernetes overlay layout system.properties per env-envservice]] in one command.

Key behaviors:
- `add-env.sh KEY=VALUE ...` is **preview-first**: prints a unified diff per env and writes nothing until re-run with `APPLY=1` (after user confirmation).
- Defaults: `SERVICE=luz-docs`, `ENVS=dev,dev-vn,dev-staging,performance,test,swissdec,prod`.
- Missing env/service dirs are warnings, never fatal.
- Git flow: on `master`/`main` it auto-creates a branch (`BRANCH=<name>` or timestamped) and commits only the touched files; on a custom branch it refuses until `ALLOW_COMMIT_ON_BRANCH=1` (ask the user first). `PUSH=1` pushes. No AI trailers.
- Repo root is configurable via `LUZ_KUBERNETES_ROOT` and auto-located + cached by `locate-repo.sh` (cache file `luz-kubernetes-root.config`).
- **Key normalization**: property-style names are auto-converted to env-var form — uppercased, `.`/`-` → `_` (e.g. `luz.docs.tenants.use-materialized` → `LUZ_DOCS_TENANTS_USE_MATERIALIZED`). Values are never touched. Implemented as `tr 'a-z.-' 'A-Z__'` (trailing `-` in a tr set is literal, not a range).

Re-running `APPLY=1` is idempotent for commit-only passes: staging is computed from `git diff` on the target files.

Gotcha: `BRANCH=<name>` creates the new branch **from the current HEAD**, not from master — when invoked while a feature/sprint branch is checked out, the new branch carries that branch's history. Check out master first if the change should be PR-able against master on its own.

## Related

- [[2 Areas/Kepler/luz_kubernetes overlay layout system.properties per env-envservice]]
- [[luz-env-config-reminder hook nudges overlay propagation for new env reads in luz repos]]

%% ai-graph-start %%

**Related notes:**
- [[luz-env-config-reminder hook nudges overlay propagation for new env reads in luz repos]]
- [[luz_kubernetes overlay layout system.properties per env-envservice]]
- [[Luz plugin repos how skills and hooks are packaged for distribution]]
- [[luz-skills-plugin packages skills by category directory listed in plugin.json]]
- [[Destructive Luz skills use a preview-first CONFIRM gate]]

**Relations:**
- luz-kubernetes-add-env skill — *propagates* — env properties
- luz-kubernetes-add-env skill — *adds or updates* — KEY=VALUE properties
- KEY=VALUE properties — *are stored in* — system.properties
- system.properties — *across* — overlay environments
- luz-kubernetes-add-env skill — *references* — luz_kubernetes overlay layout system.properties per env-envservice
- add-env.sh — *is part of* — luz-kubernetes-add-env skill
- add-env.sh — *processes* — KEY=VALUE properties
- add-env.sh — *is* — preview-first
- add-env.sh — *requires* — APPLY=1 for writing
- add-env.sh — *has default service* — SERVICE=luz-docs
- add-env.sh — *has default environments* — ENVS=dev,dev-vn,dev-staging,performance,test,swissdec,prod
- add-env.sh — *handles* — missing env/service dirs as warnings
- add-env.sh — *auto-creates branch on* — master/main branch
- add-env.sh — *commits* — touched files
- add-env.sh — *refuses commit on* — custom branch
- custom branch — *requires* — ALLOW_COMMIT_ON_BRANCH=1 for commit
- add-env.sh — *pushes changes with* — PUSH=1
- Repo root — *is configurable via* — LUZ_KUBERNETES_ROOT
- Repo root — *is auto-located by* — locate-repo.sh
- locate-repo.sh — *uses cache file* — luz-kubernetes-root.config
- luz-kubernetes-add-env skill — *includes* — Key normalization
- Key normalization — *converts* — property-style names
- property-style names — *to* — env-var form
- Key normalization — *is implemented with* — tr 'a-z.-' 'A-Z__'
- APPLY=1 — *enables* — idempotent commit-only passes
- staging — *is computed from* — git diff
- BRANCH=<name> — *creates branch from* — current HEAD
- luz-kubernetes-add-env skill — *is related to* — luz-env-config-reminder hook nudges overlay propagation for new env reads in luz repos

%% ai-graph-end %%