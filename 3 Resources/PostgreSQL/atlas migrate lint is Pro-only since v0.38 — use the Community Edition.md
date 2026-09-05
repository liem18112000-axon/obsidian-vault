---
title: "atlas migrate lint is Pro-only since v0.38 — use the Community Edition"
created: 2026-08-24
type: gotcha
status: seedling
source: "session 2026-08-24 CI failure investigation"
tags: [atlas, migrations, ci, gotcha, licensing]
---

# atlas migrate lint is Pro-only since v0.38 — use the Community Edition

As of **Atlas v0.38**, `atlas migrate lint` (and some other analysis commands) are **Pro-only** in the *default* distribution — the binary you get from `curl -sSf https://atlasgo.sh | sh`. It aborts with: "Starting with v0.38, 'atlas migrate lint' is available only to Atlas Pro users" and tells you to `atlas login`. This silently broke a CI `migrate lint` step that used to pass.

**Fix:** install the **Community Edition**, which keeps lint free + open-source (Apache-2.0):
```
curl -sSf https://atlasgo.sh | sh -s -- --community
```

**Also learned / decided (leo-customer360 CI):**
- Made the lint step `continue-on-error: true` (advisory), so Atlas licensing/tooling changes can never wedge the pipeline. The HARD gates are the blocking `dbmate up` apply-from-zero + `dbmate status` "Pending: 0" steps — those catch real breakage; lint is a nice-to-have.
- The original research doc claimed "Atlas lint is free in CI" — that's now only true with the Community Edition. Worth revisiting if Atlas restricts further.

Ref: https://atlasgo.io/blog-v038#change-in-v038-atlas-migrate-lint . Related: [[leo-customer360 uses dbmate for Postgres migrations, not Alembic]].

## Related

- [[leo-customer360 uses dbmate for Postgres migrations]]
- [[not Alembic]]


## Outcome (leo-customer360)

We **removed Atlas lint from CI entirely**, not just switched to the Community Edition. The community binary installs fine but still FAILS `migrate lint` on our migrations: lint replays them against a dev DB, and our seed migration is heavy **DML** (`INSERT INTO customer360.cdp_event_catalog ...`) which Atlas errors on — Atlas is a *schema* analyzer, not built to replay data + RLS/DO-block migrations. Same misfit that made us pick dbmate over Atlas as the runner. Replaced it with a tool-agnostic gate: `dbmate up` from zero, then TRUNCATE `schema_migrations` and replay on the populated DB asserting `Pending: 0` (idempotency). No third-party licensing in the hot path.
