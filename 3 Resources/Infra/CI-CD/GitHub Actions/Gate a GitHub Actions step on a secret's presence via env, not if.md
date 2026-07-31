---
title: "Gate a GitHub Actions step on a secret's presence via env, not if:"
created: 2026-06-14
type: lesson
status: seedling
source: "fb-info-project build-exe.yml, 2026-06-14"
tags: [github-actions, ci, secrets, gotcha]
---

# Gate a GitHub Actions step on a secret's presence via env, not if:

GitHub Actions does not expose `secrets.*` to a step-level (or job-level) `if:` expression, so you can't write `if: ${{ secrets.MY_SECRET != '' }}` to conditionally run a step. The workaround: map the secret into the step's `env:`, then branch in the shell.

In pwsh:
```yaml
- name: Maybe enable tier
  shell: pwsh
  env:
    MY_SECRET: ${{ secrets.MY_SECRET }}
  run: |
    if ([string]::IsNullOrWhiteSpace($env:MY_SECRET)) {
      Write-Host '::notice::secret absent - skipping'
      exit 0   # leave the job green
    }
    # ...secret present: set up the opt-in work, write $env:GITHUB_ENV, etc.
```

**Why it matters:** lets an opt-in feature (here, a live end-to-end test tier needing a real Facebook session supplied via the FB_SESSION_JSON secret) **skip rather than fail** when the secret is unconfigured — e.g. on forks or before the secret is set. The 'session not found' path stays green instead of erroring.

Use `exit 0` for skip; write `KEY=val` to $env:GITHUB_ENV only on the present branch so downstream steps see the flag (e.g. FB_E2E_LIVE=1).

## Related

- [[Render pytest results in GitHub UI with dorny/test-reporter java-junit]]
