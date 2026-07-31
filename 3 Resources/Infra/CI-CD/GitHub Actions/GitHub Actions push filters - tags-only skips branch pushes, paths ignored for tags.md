---
title: "GitHub Actions push filters - tags-only skips branch pushes, paths ignored for tags"
created: 2026-06-10
type: lesson
status: seedling
source: "fb-info-project build workflow, session 2026-06-10"
tags: [github-actions, ci, gotcha]
---

# GitHub Actions push filters - tags-only skips branch pushes, paths ignored for tags

On the GitHub Actions `push` event, the three filters compose in non-obvious ways:

- `tags: ['v*']` alone means the workflow runs ONLY on matching tag pushes — ordinary commits to any branch (including the default branch) never trigger it. There is no implicit run-on-default-branch behavior.
- Listing both `branches:` and `tags:` is an OR: a push matching either fires the workflow.
- `paths:` further restricts BRANCH pushes only — path filters are not evaluated for tag pushes, so a `v*` tag always builds even when the tagged commit touched none of the listed paths. This is exactly what you want for release workflows: doc-only commits to master skip the build, but tagging always produces an artifact.

Surfaced when a heavy PyInstaller build workflow (see [[Bundle Chromium inside a PyInstaller exe with PLAYWRIGHT_BROWSERS_PATH=0]]) silently never ran on master pushes because its `on.push` had only a `tags` filter.

## Related

- [[Bundle Chromium inside a PyInstaller exe with PLAYWRIGHT_BROWSERS_PATH=0]]
