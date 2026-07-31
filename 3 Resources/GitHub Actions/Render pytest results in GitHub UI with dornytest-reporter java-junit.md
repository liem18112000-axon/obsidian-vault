---
title: "Render pytest results in GitHub UI with dorny/test-reporter java-junit"
created: 2026-06-14
type: howto
status: seedling
source: "fb-info-project build-exe.yml, 2026-06-14"
tags: [github-actions, ci, pytest, testing]
---

# Render pytest results in GitHub UI with dorny/test-reporter java-junit

To surface test results in the GitHub UI (a rendered Checks report, not just buried log lines), have pytest emit a JUnit XML file and publish it with a reporter action.

```yaml
- name: Run tests
  run: python -m pytest test -ra --junitxml=test-results/junit.xml

- name: Publish test report
  uses: dorny/test-reporter@v1
  if: always()        # show results even when tests fail
  with:
    name: pytest results
    path: test-results/junit.xml
    reporter: java-junit   # pytest's JUnit XML parses under java-junit
```

**Key points:**
- pytest's `--junitxml` output is JUnit-style, so dorny's `reporter: java-junit` parses it (there is no 'pytest' reporter).
- Needs `permissions: checks: write` at workflow/job level — the action creates a Check run to render the table.
- `if: always()` so the report still publishes when the test step fails (otherwise the failing step short-circuits the job).
- dorny/test-reporter is pure JS, so it runs on windows-latest — unlike EnricoMi/publish-unit-test-result-action which is Docker/Linux-only.
- Also `actions/upload-artifact` the XML so the raw results stay downloadable.

## Related

- [[Gate a GitHub Actions step on a secret's presence via env]]
- [[not if:]]
