---
title: "Adding a step to always-on CD: provision ALL its required env, and make it skip (not die) on missing secrets"
created: 2026-08-23
type: lesson
tags: [cd, github-actions, secrets, resilience, keycloak, leo-customer360]
---

# Adding a step to always-on CD: provision ALL its required env, and make it skip (not die) on missing secrets

Two coupled failures when I added the Keycloak `sso-realm` step (bootstrap-realm.py) to CD:

1) **A step's FULL required-env set must be provisioned, not just the obvious one.** bootstrap-realm.py needs BOTH `KEYCLOAK_ADMIN_PASSWORD` (admin login) AND `KC_TEST_USER_PASSWORD` (to set the test user's password) — it `sys.exit`s on the latter. The CI/CD job provisioned only the admin password, so the step aborted: `ERROR: KC_TEST_USER_PASSWORD is required`. When adding a script to a pipeline, grep the script for every `os.environ[...]` / required var and provision them all.

2) **A newly-added step that HARD-FAILS breaks everything once the trigger becomes frequent.** The step was harmless while it rarely ran (opt-in `--deploy-uat` marker). The moment CD switched to auto-deploy on EVERY main merge, the missing secret failed EVERY deploy (app containers never even deployed, because sso-realm runs before them). Fix: for a supporting/idempotent step, SKIP-with-warning on missing secrets instead of `die`, so a missing/rotated secret degrades that one step, not the whole pipeline.

**Bash detail:** the step ran its guards inside a `( subshell )`, so a graceful skip is `exit 0` (exits only the subshell; the caller/deploy continues) — NOT `return` (invalid from within a subshell) and NOT `die`/`exit 1` (propagates as the step's failure).

**General rule:** changing a pipeline's trigger frequency (opt-in -> every-merge) retroactively raises the blast radius of every fragile step. Audit steps for hard-fail-on-missing-config before making the trigger fire more often.

Source: leo-customer360 cd.yml + deploy-all.sh sso-realm (failing run 32645483621), 2026-08-23.
