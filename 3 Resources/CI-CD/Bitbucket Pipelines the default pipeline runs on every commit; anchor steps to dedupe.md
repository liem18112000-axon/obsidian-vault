---
title: "Bitbucket Pipelines: the default pipeline runs on every commit; anchor steps to dedupe"
created: 2026-07-01
type: howto
status: seedling
source: "session 2026-07-01 (Vinnstack CI)"
tags: [bitbucket, ci, pipelines, yaml, vinnstack]
---

# Bitbucket Pipelines: the default pipeline runs on every commit; anchor steps to dedupe

Bitbucket Pipelines lives in `bitbucket-pipelines.yml` at the repo root. To run CI on EVERY commit, use the `default` pipeline — it runs for pushes on any branch that has NO more-specific `branches:` pipeline. (A `branches:` entry for a given branch OVERRIDES default for that branch; default is the catch-all.)

Pipelines must ALSO be enabled in repo Settings → Pipelines (the YAML alone isn't enough on first setup).

Reuse a step with a YAML anchor/alias to avoid duplication:
```yaml
definitions:
  caches: { npmcache: ~/.npm }
  steps:
    - step: &test
        caches: [npmcache]
        script: [npm ci, npx tsc --noEmit, npm test]
pipelines:
  default:            # every commit on every branch
    - step: *test
  pull-requests:      # also gate PRs
    "**": [ { step: *test } ]
```
Notes: cache `~/.npm` (not node_modules) and use `npm ci` for reproducible installs. `pull-requests` runs only when a PR exists; `default` already covers plain branch pushes, so listing both makes the same gate show as a required PR check. Pick a Node image matching the app (node:22 for Next 14).
