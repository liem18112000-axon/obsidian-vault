---
title: "Grep-audit env vars against code before pruning .env files"
created: 2026-07-03
type: howto
status: seedling
source: "session 2026-07-03, appsflyer-data-connector"
tags: [dotenv, env-vars, technique, audit, docker-compose]
---

# Grep-audit env vars against code before pruning .env files

Before simplifying or pruning a `.env` / `.env.example`, build the ground-truth list of live variables by grepping the codebase for actual env-var **reads** — never trust the env file itself, it accumulates dead keys as the code evolves.

Two sweeps cover it:

1. Source reads: `rg -o "(APPSFLYER|MINIO|KAFKA|AWS)_[A-Z0-9_]+" src/` (adapt prefixes) catches `os.environ` / config-model lookups.
2. Substitutions: docker-compose files, CI workflows, and scripts consume `${VAR}` without the app ever reading it (e.g. `MINIO_ROOT_USER` only used by the compose file) — grep those too, or you will delete a var that only infra uses.

A var referenced nowhere in either sweep is dead and safe to drop (in appsflyer-data-connector this exposed `LEO_CDP_INGEST_*`, `APPSFLYER_APP_IDS`, `HOST_PROJECT_DIR`, `AIRFLOW_FERNET_KEY` as leftovers from replaced designs). The same sweep also shows vars the code reads that the example file is *missing* — pruning and back-filling are the same audit.

Gotcha: real `.env` files attract non-env squatters (account credentials, console URLs pasted as "notes to self"). Do not silently delete those — they may exist nowhere else; flag them and tell the owner to move them to a password manager.

Related: keep comments on their own lines in `.env` — inline `# ...` after a value is parsed differently across dotenv implementations and docker compose.


Also prune vars that merely **restate the code default** (e.g. `FOO_STORE=local` when the code does `os.environ.get("FOO_STORE", "local")`) — they add noise and drift risk. Keep them only as commented flip-hints (`# FOO_STORE=s3`).
