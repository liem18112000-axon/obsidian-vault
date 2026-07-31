---
ai_hash: a10fb78cb5004f24
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-26
entities: []
source: session 2026-07-26
status: seedling
tags:
- github-actions
- monorepo
- ci-cd
title: 'GitHub Actions in a monorepo: workflows live at repo root, scope per project
  with paths filters'
type: howto
---

# GitHub Actions in a monorepo: workflows live at repo root, scope per project with paths filters

GitHub only runs workflow files located at the repository root under .github/workflows/ — they cannot live inside a subproject folder. In a monorepo, keep each subproject's pipeline independent by giving its workflows a `paths:` trigger filter (e.g. paths: ['core-customer360/**', '.github/workflows/customer360-*.yml']).

Gotcha: `paths` (and `branches`) filters are PER-WORKFLOW, not per-job — you cannot path-filter individual jobs. If you merge multiple subprojects into one workflow you lose per-path gating and must gate jobs by `if:` on event/branch instead. Keeping one workflow per subproject preserves clean path filters.

DRY across CI and CD: extract shared jobs (e.g. the test suite) into a reusable workflow with `on: workflow_call`, then call it from both with `jobs.<id>.uses: ./.github/workflows/<file>.yml`. A called workflow's permissions are capped by the caller, so grant the needed scopes (e.g. checks: write) in the caller too.

## Related

- [[pytest results into a GitHub Actions build via dorny test-reporter]]

%% ai-graph-start %%

**Related notes:**
- [[pytest results into a GitHub Actions build via dorny test-reporter]]
- [[GitHub Actions push filters - tags-only skips branch pushes, paths ignored for tags]]
- [[workflow_dispatch Run button only appears on the default branch - use gh workflow run --ref to dispatch from a feature branch]]
- [[Publish a Docker image to GHCR from GitHub Actions with GITHUB_TOKEN]]
- [[secrets context is not available in GitHub Actions if conditions]]

%% ai-graph-end %%