---
title: "A workflow_run-triggered GitHub workflow always runs the version on the DEFAULT branch"
created: 2026-08-23
type: gotcha
tags: [github-actions, workflow_run, cd, gotcha]
---

# A workflow_run-triggered GitHub workflow always runs the version on the DEFAULT branch

GitHub Actions `on: workflow_run` (a workflow that fires after another workflow completes) only ever triggers if the workflow file exists on the repo's DEFAULT branch, and the version that RUNS is the one on the default branch — NOT the version on the branch/PR that the triggering run came from.

Consequence: editing a workflow_run-triggered workflow (e.g. a CD pipeline that fires after CI) on a feature branch has NO effect until it's MERGED to the default branch. You can't validate the new trigger behavior from the PR; it only takes effect post-merge, and the merge commit's own CI run is the first to exercise it.

(Contrast: `on: push`/`on: pull_request` workflows run the version AT the pushed/PR ref, so branch edits take effect immediately for those.)

Practical: test workflow_run logic changes by merging to a throwaway default-like branch, or by refactoring the risky logic into a script the workflow calls (the script can be tested on the branch) so the YAML change stays trivial.

Source: leo-customer360 cd.yml (workflow_run-based CD), 2026-08-23.
