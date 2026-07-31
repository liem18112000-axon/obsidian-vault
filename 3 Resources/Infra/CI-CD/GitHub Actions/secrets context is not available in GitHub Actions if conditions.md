---
title: "secrets context is not available in GitHub Actions if conditions"
created: 2026-06-06
updated: 2026-07-31
type: lesson
status: seedling
source: "leo-cdp-framework ci-cd.yml 2026-06-06; fb-info-project build-exe.yml 2026-06-14; accesstrade_integration provision workflow 2026-06-14"
tags: [github-actions, secrets, ci, ci-cd, gcp, workload-identity, gotcha]
---

# secrets context is not available in GitHub Actions if conditions

The **`secrets` context is not available in `if:`** — neither job- nor step-level. `if: ${{ secrets.FOO != '' }}` does not error; it evaluates as if the secret were empty, so the step/job silently never runs (or always runs). Secrets ARE allowed in `env:`, in `run:`, and in step `with:` expressions — just not in `if:`.

**Why it matters:** an opt-in job whose secret is unset (forks, Dependabot PRs, GCP not wired up yet) should SKIP green, not hard-fail. `google-github-actions/auth@v2` fails with `must specify exactly one of workload_identity_provider or credentials_json` when both inputs resolve to empty secrets, reddening every push.

**Fix A — same job: hoist to `env`, then test `env.*`** (`env` IS an allowed `if:` context):

```yaml
env:
  RECIPIENTS: ${{ secrets.DEV_TEAM_EMAILS }}
steps:
  - if: ${{ env.RECIPIENTS != '' }}
    uses: some/action@v1
```

**Fix B — branch inside the shell** when the step itself must decide. Map the secret into the step's `env:` and exit early; `exit 0` leaves the job green:

```yaml
- shell: pwsh
  env: { MY_SECRET: ${{ secrets.MY_SECRET }} }
  run: |
    if ([string]::IsNullOrWhiteSpace($env:MY_SECRET)) {
      Write-Host '::notice::secret absent - skipping'; exit 0
    }
```

Write `KEY=val >> $GITHUB_ENV` only on the present branch so downstream steps see the opt-in flag (e.g. `FB_E2E_LIVE=1`).

**Fix C — gate a whole JOB via a preflight job output.** Job-level `if:` CAN read `needs.*`, so compute the boolean in a tiny job first:

```yaml
jobs:
  preflight:
    runs-on: ubuntu-latest
    outputs: { ready: ${{ steps.cfg.outputs.ready }} }
    steps:
      - id: cfg
        env: { WIF: ${{ secrets.GCP_WIF_PROVIDER }}, SA: ${{ secrets.GCP_SERVICE_ACCOUNT }} }
        run: |
          if [ -n "$WIF" ] && [ -n "$SA" ]; then echo ready=true >> "$GITHUB_OUTPUT";
          else echo ready=false >> "$GITHUB_OUTPUT"; echo '::notice::skipped — no creds'; fi
  provision:
    needs: preflight
    if: needs.preflight.outputs.ready == 'true'
```

The gated job auto-activates the moment the secret is added — no workflow edit needed.

Adjacent trick: you *can* compare secrets inside `with:` — `credentials_json: ${{ secrets.WIF == '' && secrets.CREDS || '' }}` satisfies an "exactly one of" input.

## Related

- [[Publish a Docker image to GHCR from GitHub Actions with GITHUB_TOKEN]]
- [[pytest results into a GitHub Actions build via dorny test-reporter]]
- [[GitHub Actions continue-on-error step-level goes green, job-level stays red]]
- [[LEO CDP CI provisions deps CI-natively, pinned to devops-script versions for parity]]
