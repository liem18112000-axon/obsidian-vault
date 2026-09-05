---
title: "CD didn't fire on an infra-only merge (CI paths-ignore); re-run a workflow_run deploy via workflow_dispatch, not run rerun"
created: 2026-08-23
type: gotcha
tags: [github-actions, cd, workflow_run, paths-ignore, leo-customer360]
---

# CD didn't fire on an infra-only merge (CI paths-ignore); re-run a workflow_run deploy via workflow_dispatch, not run rerun

Two GitHub Actions trigger gotchas hit while shipping a CD hotfix:

1) **A CD that fires via `workflow_run: [CI]` only runs if CI ran.** If CI has `paths-ignore` (e.g. ignores `.github/**`, `deployments/**`, `docs/**` so only service-code changes build images), then merging an INFRA/workflow-only change to main triggers NEITHER CI NOR CD — even on a repo configured to "deploy uat on every merge". "Every merge" really means "every merge that trips CI". Don't wait for a CD that will never come; check whether CI actually ran (`gh api .../actions/runs?head_sha=<sha>`).

2) **To re-run a deploy with a FIX, use `workflow_dispatch`, NOT `gh run rerun` on the failed run.** A `workflow_run`-triggered CD job typically checks out `github.event.workflow_run.head_sha` (the ORIGINAL commit) — so re-running the failed run replays the OLD, still-broken code. `workflow_dispatch` instead checks out `github.sha` = the default branch (which now has the fix). So: `gh workflow run cd.yml -f environment=uat -f image_tag=latest -f services=...` deploys the fixed code; re-running the old run just fails again.

Watch a dispatched run to completion with `gh run watch <id> --exit-status`.

Source: leo-customer360 CD hotfix #21 (sso-realm), 2026-08-23.
