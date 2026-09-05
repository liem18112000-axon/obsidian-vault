---
title: "Publish a GitHub Actions job result to the run Summary with an always() step"
created: 2026-08-24
type: howto
status: seedling
source: "session 2026-08-24 dbmate CI"
tags: [github-actions, ci, step-summary, bash]
---

# Publish a GitHub Actions job result to the run Summary with an always() step

To show a result on a GitHub Actions **run Summary page** (not just buried in logs), append markdown to the `$GITHUB_STEP_SUMMARY` file. To make it appear **regardless of pass/fail**, put it in a dedicated final step with `if: always()`.

**Key trick — survive earlier step failures:** the summary step reads each prior step's result via `${{ steps.<id>.outcome }}` (`success`/`failure`/`skipped`), and reads any captured data from files the prior steps wrote to `$RUNNER_TEMP`. So give the real steps an `id:`, have them `tee` their output to `$RUNNER_TEMP/foo.log` and push key values with `echo "k=v" >> $GITHUB_OUTPUT` BEFORE they can fail. A failing step still aborts the job (good), but the always() step then renders a table + a collapsible `<details>` log block.

**Gotcha capturing a command's exit under `bash -e`** (GitHub's default `run:` shell): `out=$(cmd 2>&1)` aborts the whole step if cmd fails. Use `out=$(cmd 2>&1) && rc=0 || rc=$?` so set -e treats the failure as handled, then `exit "$rc"` after logging.

Pattern (leo-customer360 ci.yml migrations job):
```yaml
- id: apply
  run: |
    out=$(docker run … up 2>&1) && rc=0 || rc=$?
    printf '%s\n' "$out" | tee "$RUNNER_TEMP/apply.log"
    echo "count=$(grep -c '^Applied:' "$RUNNER_TEMP/apply.log" || true)" >> "$GITHUB_OUTPUT"
    exit "$rc"
- name: Write run summary
  if: always()
  run: |
    st(){ case "$1" in success) echo '✅ pass';; failure) echo '❌ fail';; *) echo '⏭️ skipped';; esac; }
    { echo '## …'; echo "| Apply | $(st "${{ steps.apply.outcome }}") | ${{ steps.apply.outputs.count }} |"; } >> "$GITHUB_STEP_SUMMARY"
```
Related: [[leo-customer360 uses dbmate for Postgres migrations, not Alembic]].

## Related

- [[leo-customer360 uses dbmate for Postgres migrations]]
- [[not Alembic]]
