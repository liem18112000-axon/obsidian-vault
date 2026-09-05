---
title: "Chain a CD workflow after CI with workflow_run, gating on conclusion and ref"
created: 2026-08-20
type: howto
status: seedling
source: "session 2026-08-20, leo-customer360 cd.yml"
tags: [github-actions, cd, workflow-run, ci-cd, howto]
---

# Chain a CD workflow after CI with workflow_run, gating on conclusion and ref

To run deploys only after tests pass, put CD in a **separate** workflow triggered by the CI workflow finishing, not by push:

```yaml
on:
  workflow_run:
    workflows: ["CI"]     # must match the CI workflow's `name:` exactly
    types: [completed]
```

Then gate inside the job — `workflow_run` fires on **every** CI completion (success OR failure), so you MUST check the conclusion:

```yaml
if: ${{ github.event.workflow_run.conclusion == 'success' }}
```

Pick the target env from `github.event.workflow_run.head_branch` (this holds the branch name for branch pushes and the **tag name** for tag pushes): `main` → uat, `^v[0-9]+\.` → prod. Deploy the exact validated commit with `actions/checkout` `ref: ${{ github.event.workflow_run.head_sha }}` (a bare checkout would grab the default branch tip, not what CI tested). Bind the job to a GitHub **Environment** (`environment: prod`) so required-reviewer protection gates the release.

Gotchas: (1) a `workflow_run`-triggered workflow only runs when its file is on the **default branch** — CD won't trigger from a feature branch until merged. (2) `head_branch` for tags is generally the tag name, but verify; if unreliable, resolve tags at `head_sha` via `gh api`. Companion pattern for the CI side: build ALL services on a release tag but only changed services on a branch (switch the matrix source on `startsWith(github.ref, 'refs/tags/v')`). Context: [[leo-customer360 CD builds images on the VM instead of pulling from GHCR (CI/CD gap)]].

## Related

- [[leo-customer360 CD builds images on the VM instead of pulling from GHCR (CI/CD gap)]]

## Opt-in per-commit deploy marker

To make a `workflow_run` deploy **opt-in per commit** (deploy on demand, not on every push to the branch), read the triggering commit message from `github.event.workflow_run.head_commit.message` and match a marker in its **title** (first line only, so a marker in the body doesn't count):

```yaml
env:
  MSG: ${{ github.event.workflow_run.head_commit.message }}
run: |
  TITLE="$(printf '%s\n' "$MSG" | head -n1)"
  [[ "$TITLE" == *"--deploy-uat"* ]] && echo "deploy=true"
```

`head_commit` is the push's head commit and carries `.message`. Used in leo-customer360 `cd.yml` so uat deploys only when the commit title contains `--deploy-uat` (prod stays tag-triggered).
