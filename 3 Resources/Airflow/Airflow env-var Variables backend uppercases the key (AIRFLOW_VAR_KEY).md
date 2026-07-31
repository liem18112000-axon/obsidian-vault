---
title: "Airflow env-var Variables backend uppercases the key (AIRFLOW_VAR_<KEY>)"
created: 2026-06-24
type: gotcha
status: seedling
source: "appsflyer-data-connector PR #1 review, 2026-06-24"
tags: [airflow, variables, secrets-backend, gotcha, jinja-templating]
---

# Airflow env-var Variables backend uppercases the key (AIRFLOW_VAR_<KEY>)

Airflow's environment-variable Variables secrets backend resolves \`Variable.get("my_key")\` — and the Jinja accessor \`{{ var.value.my_key }}\` / \`{{ var.value.get("my_key", default) }}\` — by reading the OS environment variable named \`AIRFLOW_VAR_\` + **key.upper()**.

So a lowercase template key and an uppercase env var are the **two matching halves of the same convention**, not a mismatch:

- \`{{ var.value.minio_access_key }}\` → \`Variable.get("minio_access_key")\` → reads \`AIRFLOW_VAR_MINIO_ACCESS_KEY\`.
- A docker-compose stack that exports \`AIRFLOW_VAR_MINIO_ACCESS_KEY\` is therefore correctly consumed by a DAG templating \`var.value.minio_access_key\` — no fallback to the default occurs.

**Gotcha / why it matters:** a Copilot PR review flagged "the DAG reads \`var.value.minio_access_key\` (lowercase) but compose sets \`AIRFLOW_VAR_MINIO_ACCESS_KEY\` (uppercase), so creds template empty" — that was a **false positive**, because the backend upper-cases the key during lookup. Knowing this convention saves you from "fixing" working config or chasing phantom empty-Variable bugs.

Source: \`airflow/secrets/environment_variables.py\` → \`get_variable\` does \`os.environ.get(VAR_ENV_PREFIX + key.upper())\`, where \`VAR_ENV_PREFIX = "AIRFLOW_VAR_"\`.
