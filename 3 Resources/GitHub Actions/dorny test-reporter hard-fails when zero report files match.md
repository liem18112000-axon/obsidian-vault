---
title: "dorny test-reporter hard-fails when zero report files match"
created: 2026-06-06
type: lesson
status: seedling
source: "leo-cdp-framework ci-cd.yml debugging 2026-06-06"
tags: [github-actions, testing, junit, ci, gotcha]
---

# dorny test-reporter hard-fails when zero report files match

The `dorny/test-reporter@v1` action **hard-fails the step** with `No test report files were found` when its `path:` glob matches **zero files** — and `fail-on-error: false` does NOT suppress this (that flag only controls behavior when a *parsed report contains test failures*, not the missing-files case).

**When this bites:** a build that exports test XML on a best-effort basis (e.g. a Dockerfile that runs `gradle test ... || true` and may legitimately produce no XML) will intermittently have an empty results dir, turning the report step red even though nothing is actually broken.

**Fix:** gate the reporter (and any `upload-artifact` of the same files) on whether XML actually exists. In the prior step, set an output and branch on it:

```bash
if [ -n "$(find ./test-results -name "*.xml" -print -quit)" ]; then
  echo "found=true"  >> "$GITHUB_OUTPUT"
else
  echo "found=false" >> "$GITHUB_OUTPUT"
  echo "::notice::No JUnit XML exported — skipping the test report."
fi
```
then `if: always() && steps.tests.outputs.found == "true"` on the reporter.

## Related
- [[LEO CDP CI provisions deps CI-natively, pinned to devops-script versions for parity]]

## Related

- [[LEO CDP CI provisions deps CI-natively]]
- [[pinned to devops-script versions for parity]]
