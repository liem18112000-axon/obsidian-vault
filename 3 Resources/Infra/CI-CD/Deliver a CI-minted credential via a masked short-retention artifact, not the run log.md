---
ai_hash: f6e9a0003bf1fc4d
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-04
entities: []
source: session 2026-07-04 fb-info-project sign-license.yml
status: seedling
tags:
- github-actions
- secrets
- ci
- security
title: Deliver a CI-minted credential via a masked short-retention artifact, not the
  run log
type: howto
---

# Deliver a CI-minted credential via a masked short-retention artifact, not the run log

When a CI job produces a credential (a signed license token, an API key, a cert), do NOT print it to the run log - Actions logs are visible to anyone with read access and are retained. Instead:

1. Capture the tool output to a file rather than tee-ing it to stdout.
2. Extract the secret line and `echo "::add-mask::$token"` so any later incidental echo is redacted (masking is not retroactive - mask BEFORE the value could be printed).
3. Print only the non-secret parts (e.g. the license payload) to the log for the operator's records.
4. Deliver the credential as an uploaded artifact with a short `retention-days` (e.g. 1) so it does not linger in Actions storage.

Gotcha: `tee` writes to the log immediately, so masking afterwards is too late - redirect to a file (`> out.txt`) and read it back instead. On GNU coreutils `head -n -1 file` prints everything except the last (token) line.

Related: [[CI token-signing workflow - verify the secret private key matches the pinned public key before signing]]

%% ai-graph-start %%

**Related notes:**
- [[GitHub Actions masks a secret's VALUE everywhere - a plaintext field logging as means it equals a secret]]
- [[Pass a value between GitHub Actions steps via GITHUB_OUTPUT and steps.id.outputs]]
- [[CI token-signing workflow - verify the secret private key matches the pinned public key before signing]]
- [[masking-token-in-fetch-command-breaks-downstream]]
- [[Pipe a GCP service-account key straight into a GitHub secret without leaking it]]

%% ai-graph-end %%