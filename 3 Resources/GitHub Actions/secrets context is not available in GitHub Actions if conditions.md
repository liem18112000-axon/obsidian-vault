---
title: "secrets context is not available in GitHub Actions if conditions"
created: 2026-06-06
type: lesson
status: seedling
source: "leo-cdp-framework ci-cd.yml debugging 2026-06-06"
tags: [github-actions, secrets, ci, gotcha]
---

# secrets context is not available in GitHub Actions if conditions

The **`secrets`** context is **not available in `if:` conditions** in GitHub Actions (neither job-level nor step-level). Writing `if: ${{ secrets.FOO != '' }}` does not error loudly — it evaluates as if the secret were empty, so the step/job silently never runs (or always runs, depending on the comparison). This is why a workflow that wants to gate on "is this secret configured?" uses a separate gate job that reads the secret in a `run:` step and exposes a boolean via `outputs` + `needs` (the `ai-check` / `detect` pattern).

**Lightweight fix within one job:** secrets *are* allowed in `env:`. Hoist the secret to job- (or step-) level `env`, then test `env.FOO` in the `if:` — `env` *is* an allowed context in `if`.

```yaml
env:
  RECIPIENTS: ${{ secrets.DEV_TEAM_EMAILS }}
steps:
  - if: ${{ env.RECIPIENTS != '' }}
    uses: some/action@v1
```

Use this for graceful degradation (skip email/publish steps cleanly when their secret is unset instead of hard-failing).

## Related
- [[LEO CDP CI provisions deps CI-natively, pinned to devops-script versions for parity]]

## Related

- [[LEO CDP CI provisions deps CI-natively]]
- [[pinned to devops-script versions for parity]]
