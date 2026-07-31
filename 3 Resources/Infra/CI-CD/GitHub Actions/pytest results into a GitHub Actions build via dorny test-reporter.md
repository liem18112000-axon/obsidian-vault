---
title: "pytest results into a GitHub Actions build via dorny test-reporter"
created: 2026-07-26
type: lesson
status: seedling
source: "session 2026-07-26"
tags: [github-actions, pytest, testing, ci-cd]
---

# pytest results into a GitHub Actions build via dorny test-reporter

To surface pytest results inside a GitHub Actions run: pytest emits standard JUnit XML with `--junitxml=test-results/junit.xml`, then publish it with `dorny/test-reporter@v1` using `reporter: java-junit` (pytest's JUnit is compatible with the java-junit parser). This creates a Checks-tab report annotated on the build.

Key patterns:
- Put the publish step behind `if: always()` so the report appears even when tests FAIL (the test step already exited non-zero).
- Set the reporter's `fail-on-error: false` and let pytest's own exit code drive job pass/fail — this also avoids hard failures on fork PRs where GITHUB_TOKEN is read-only and the reporter can't write a check.
- The job/workflow needs `permissions: checks: write`.

Related gotcha: a workflow secret cannot be referenced in a job/step `if:` condition. To gate a job on whether a secret is configured, hoist it into `env:` first (env CAN read secrets), then branch on the env var in a run step.

## Related

- [[GitHub Actions in a monorepo: workflows live at repo root]]
- [[scope per project with paths filters]]
