---
title: "run_it.sh TENANT_ID is a shell parameter, not an .env value"
created: 2026-07-12
type: lesson
status: seedling
source: "vinnstack BDD Run Tests TENANT_ID override bugfix, 2026-07-12"
tags: [bash, env-vars, precedence, gotcha, vinnstack, luz-docs]
---

# run_it.sh TENANT_ID is a shell parameter, not an .env value

In the vinnstack fork of `run_it.sh` (luz-docs-integration-test runner), `TENANT_ID` is NOT read from the suite's `.env` file when running in local mode — it is a top-level bash script parameter (`TENANT_ID="${TENANT_ID:-<hardcoded-default>}"`, evaluated once at script start from a pre-existing shell/process env var or a `KEY=VALUE` CLI arg), and it is re-exported verbatim right before invoking `behave`. The script even logs this precedence explicitly: `"[it] .env found — applying TENANT_ID=$TENANT_ID for this run (env var overrides .env)"`.

Practical consequence: writing `TENANT_ID=<value>` into the worktree's `.env` file has ZERO effect on the actual test run — the script never sources `.env` into its own shell in local mode. To actually override the tenant, `TENANT_ID` must be set as a real environment variable on the process that invokes `run_it.sh` (or passed as a `KEY=VALUE` positional arg), so bash's `${TENANT_ID:-default}` expansion picks up the real value instead of falling through to the hardcoded default.

This is a special case, not the general rule for that script's other config values — CLIENT_ID/CLIENT_SECRET/USER_ID/PASSWORD/the various *_URL keys are only referenced inside `write_default_env()`'s heredoc (written to `.env` only when `.env` doesn't already exist) and consumed later by the Python test suite via its own `.env` loading (e.g. python-dotenv) — those DO respect whatever is written into `.env`. TENANT_ID is the one variable that also has its own bash-level default-and-reexport logic, which bypasses `.env` entirely.

Fix applied in vinnstack's BDD Run Tests feature (lib/bdd/verifyRunner.ts, `applyConfiguredEnvVars`): in addition to writing the operator's configured key/value pairs into the worktree's `.env` file, also return them so callers merge them into the actual child-process environment passed to the spawned script. That satisfies TENANT_ID's shell-parameter precedence directly, rather than relying on a `.env` file the script never reads for that one key.

General lesson: when a bash script both (a) defines `VAR="${VAR:-default}"` at the top AND (b) writes/reads a `.env` file elsewhere, don't assume the `.env` file is authoritative for every variable that also appears in it — some variables can be "shell-parameter-only" and effectively immune to `.env` overrides. Always trace where a specific variable is actually consumed (grep for its usage across the whole script) before assuming a config file changes its value.

Related: [[Tailwind class-string order does not determine cascade override]] — another case this session of "the config you set doesn't take effect because something else wins by precedence, silently."

## Related

- [[Tailwind class-string order doesn't determine cascade override]]
