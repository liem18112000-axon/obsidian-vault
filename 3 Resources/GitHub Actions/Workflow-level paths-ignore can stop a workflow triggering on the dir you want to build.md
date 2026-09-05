---
title: "Workflow-level paths-ignore can stop a workflow triggering on the dir you want to build"
created: 2026-08-20
type: lesson
status: seedling
source: "session 2026-08-20 leo-customer360 CI"
tags: [github-actions, ci, gotcha]
---

# Workflow-level paths-ignore can stop a workflow triggering on the dir you want to build

A workflow-level `paths-ignore` skips the **entire workflow run** when *all* changed files match its patterns. The trap: if you list a directory there that you actually want to build/test, a commit that touches only that directory triggers **no jobs at all** — so the build for it never runs.

Example: `ci.yml` had `paths-ignore: [frontend-admin/**]` (added when frontend had no tests). Later we wanted to build the frontend image on changes — but a frontend-only commit would not start the workflow. Fix: remove that entry from `paths-ignore` so the workflow triggers, then scope the *test* job separately if you still want to skip it there.

Rule of thumb: `paths-ignore` is about *whether the workflow starts*, not per-job filtering. Use per-job path filters (e.g. dorny/paths-filter) for "run this job only when X changed"; reserve `paths-ignore` for dirs that should never start CI (docs, wireframes).

Related: [[Feed dorny/paths-filter changes output into a build matrix for selective monorepo builds]].

## Related

- [[Feed dorny/paths-filter changes output into a build matrix for selective monorepo builds]]
