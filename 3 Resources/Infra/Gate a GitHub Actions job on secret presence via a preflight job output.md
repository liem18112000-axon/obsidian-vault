---
title: "Gate a GitHub Actions job on secret presence via a preflight job output"
created: 2026-06-14
type: howto
status: seedling
source: "session 2026-06-14, accesstrade_integration provision workflow"
tags: [github-actions, ci-cd, secrets, gcp, workload-identity]
---

# Gate a GitHub Actions job on secret presence via a preflight job output

**You cannot reference `secrets.*` in a job-level `if:` in GitHub Actions — secrets aren't available in that context. To make a job run only when a secret is configured, add a tiny `preflight` job that reads the secret (secrets ARE allowed in `env:`/`run:`), writes a `ready=true/false` step output, and gate the real job with `needs: preflight` + `if: needs.preflight.outputs.ready == 'true'`.**

Why this matters: an action like `google-github-actions/auth@v2` hard-fails ('must specify exactly one of workload_identity_provider or credentials_json') when its inputs resolve to empty unset secrets. Without gating, every push that touches the path turns the workflow red even though GCP simply isn't wired up yet. Gating makes the job **skip (green)** when unconfigured and **auto-activate** the moment the secret is added — no workflow edit needed.

Pattern:
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
    ...
```

Related GHA expression trick: pass a credential only when another is unset to satisfy 'exactly one' — `credentials_json: ${{ secrets.WIF == '' && secrets.CREDS || '' }}` (you CAN compare secrets in step/with expressions, just not in job `if`). Relates to [[Publish a Docker image to GHCR from GitHub Actions with GITHUB_TOKEN]].
