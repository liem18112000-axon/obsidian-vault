---
title: "Fail-open service config: render the instance config at container start from backend probes"
created: 2026-09-03
type: lesson
status: seedling
source: "leo-customer360 Phase 0 fail-open, 2026-09-03"
tags: [deployment, resilience, docker, kubernetes, dagster, pattern]
---

# Fail-open service config: render the instance config at container start from backend probes

To make a service tolerate a missing/unreachable backing store (DB, object storage, cache) **without failing to start**, render its configuration **at container start** from live probes instead of baking a static config that hard-references those backends.

Pattern (used for the Dagster instance config on Docker/vServer): an `entrypoint.sh` runs a small `render_*.py` that, for each backend, PROBES reachability (e.g. `psycopg2.connect(..., connect_timeout=5)`; S3 `head_bucket` with short timeouts + no retries) and writes the config block only when the probe succeeds — otherwise omits it so the app uses its **local default** (SQLite / local files). Then `exec "$@"` launches the real process. Every probe is wrapped in try/except and the script NEVER raises: worst case it writes a comment-only config and the app runs on all defaults.

Why this beats a static config with `{ env: X }` refs: env-var interpolation fails hard when a var is unset, and a static file cannot conditionally include a storage block. Rendering makes the choice at runtime, per environment, with no separate config artifacts per env.

Consequences to call out:
- **Fail-open trades durability for availability:** falling back to ephemeral local storage keeps the service UP but may lose data written during the outage. Document it; auto-recover by redeploying once the backend is back (the renderer switches back automatically).
- **Wire it as ENTRYPOINT + CMD**, not as the k8s `command:` — in Kubernetes `command:` OVERRIDES the image ENTRYPOINT, bypassing the renderer. Either keep the image ENTRYPOINT and vary only `args:`, or set `command: ["/app/entrypoint.sh"]` explicitly.
- One-off maintenance tools baked in the same image must run with `--entrypoint <bin>` to skip the render side-effect.

## Related
[[Dagster auto-creates its tables but not the database]]
[[Dagster S3ComputeLogManager: credentials via boto3 env, path-style via AWS config file]]

## Related

- [[Dagster auto-creates its tables but not the database]]
