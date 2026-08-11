---
ai_hash: 376ba24ee995f310
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-03
entities: []
source: session 2026-07-03, appsflyer-data-connector
status: seedling
tags:
- dotenv
- env-vars
- technique
- audit
- docker-compose
title: Grep-audit env vars against code before pruning .env files
type: howto
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

%% ai-graph-start %%

**Related notes:**
- [[Module-level load_dotenv lets unit tests hit real cloud credentials]]
- [[MinIO server creds (ROOT_USERPASSWORD) are distinct env vars from the S3 client creds (ACCESS_KEYSECRET_KEY)]]
- [[AppsFlyer connector S3 config is vStorage-only - VSTORAGE_ env vars]]
- [[AppsFlyer connector dropped Kafka sink and S3 raw landing]]
- [[Airflow env-var Variables backend uppercases the key (AIRFLOW_VAR_KEY)]]

%% ai-graph-end %%