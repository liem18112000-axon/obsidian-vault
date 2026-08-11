---
ai_hash: be99031299c71999
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-14
entities: []
source: fb-info-project build-exe.yml 2026-06-14; session 2026-07-26
status: seedling
tags:
- github-actions
- pytest
- testing
- ci
- ci-cd
title: pytest results into a GitHub Actions build via dorny test-reporter
type: howto
updated: 2026-07-31
---

# pytest results into a GitHub Actions build via dorny test-reporter

Surface pytest results as a rendered **Checks-tab report** (not buried log lines): have pytest emit JUnit XML, then publish it with `dorny/test-reporter@v1` using `reporter: java-junit` — pytest's `--junitxml` output parses under the java-junit parser (there is no `pytest` reporter).

```yaml
- name: Run tests
  run: python -m pytest test -ra --junitxml=test-results/junit.xml

- name: Publish test report
  uses: dorny/test-reporter@v1
  if: always()              # publish even when the test step exited non-zero
  with:
    name: pytest results
    path: test-results/junit.xml
    reporter: java-junit
    fail-on-error: false    # let pytest's exit code drive job pass/fail
```

Key points:
- Needs `permissions: checks: write` at workflow/job level — the action creates a Check run.
- `fail-on-error: false` also avoids a hard failure on **fork PRs**, where `GITHUB_TOKEN` is read-only and the reporter cannot write the check.
- dorny/test-reporter is pure JS, so it runs on `windows-latest` — unlike `EnricoMi/publish-unit-test-result-action` (Docker/Linux-only).
- Also `actions/upload-artifact` the XML so raw results stay downloadable.

## Related

- [[dorny test-reporter hard-fails when zero report files match]]
- [[secrets context is not available in GitHub Actions if conditions]]
- [[GitHub Actions in a monorepo workflows live at repo root, scope per project with paths filters]]

%% ai-graph-start %%

**Related notes:**
- [[dorny test-reporter hard-fails when zero report files match]]
- [[GitHub Actions in a monorepo workflows live at repo root, scope per project with paths filters]]
- [[Export build artifacts from a multi-stage Docker build via a scratch stage + buildx --output]]
- [[secrets context is not available in GitHub Actions if conditions]]

%% ai-graph-end %%