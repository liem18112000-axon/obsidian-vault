---
ai_hash: 3d4cd37adfe09df3
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-10
entities: []
source: fb-info-project build workflow, session 2026-06-10
status: seedling
tags:
- github-actions
- ci
- gotcha
title: GitHub Actions push filters - tags-only skips branch pushes, paths ignored
  for tags
type: lesson
---

# GitHub Actions push filters - tags-only skips branch pushes, paths ignored for tags

On the GitHub Actions `push` event, the three filters compose in non-obvious ways:

- `tags: ['v*']` alone means the workflow runs ONLY on matching tag pushes — ordinary commits to any branch (including the default branch) never trigger it. There is no implicit run-on-default-branch behavior.
- Listing both `branches:` and `tags:` is an OR: a push matching either fires the workflow.
- `paths:` further restricts BRANCH pushes only — path filters are not evaluated for tag pushes, so a `v*` tag always builds even when the tagged commit touched none of the listed paths. This is exactly what you want for release workflows: doc-only commits to master skip the build, but tagging always produces an artifact.

Surfaced when a heavy PyInstaller build workflow (see [[Bundle Chromium inside a PyInstaller exe with PLAYWRIGHT_BROWSERS_PATH=0]]) silently never ran on master pushes because its `on.push` had only a `tags` filter.

## Related

- [[Bundle Chromium inside a PyInstaller exe with PLAYWRIGHT_BROWSERS_PATH=0]]

%% ai-graph-start %%

**Related notes:**
- [[Publish every CI build via a rolling latest pre-release on GitHub]]
- [[GitHub Actions in a monorepo workflows live at repo root, scope per project with paths filters]]
- [[workflow_dispatch Run button only appears on the default branch - use gh workflow run --ref to dispatch from a feature branch]]
- [[GitHub Actions artifact quota is org-wide; Release assets bypass it]]
- [[CI build Docker image on every run, push only on non-PR]]

%% ai-graph-end %%