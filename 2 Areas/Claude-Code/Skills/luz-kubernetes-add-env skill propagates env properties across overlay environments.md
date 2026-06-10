---
title: "luz-kubernetes-add-env skill propagates env properties across overlay environments"
created: 2026-06-10
type: howto
status: seedling
source: "session 2026-06-10"
tags: [claude-code, skill, luz, kubernetes, config]
---

# luz-kubernetes-add-env skill propagates env properties across overlay environments

The `luz-kubernetes-add-env` skill (`~/.claude/skills/luz-kubernetes-add-env/`) adds or updates `KEY=VALUE` properties in `system.properties` across all overlay environments of [[luz_kubernetes overlay layout: system.properties per env-<env>/<service>]] in one command.

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

- [[luz_kubernetes overlay layout: system.properties per env-<env>/<service>]]
- [[luz-env-config-reminder hook nudges overlay propagation for new env reads in luz repos]]
