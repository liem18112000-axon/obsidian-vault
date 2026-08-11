---
ai_hash: bf53ff2cbff15d91
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-04
entities: []
source: session 2026-07-04 fb-info-project run 28708059025
status: seedling
tags:
- github-actions
- secrets
- debugging
- gotcha
title: GitHub Actions masks a secret's VALUE everywhere - a plaintext field logging
  as *** means it equals a secret
type: lesson
---

# GitHub Actions masks a secret's VALUE everywhere - a plaintext field logging as *** means it equals a secret

GitHub Actions automatically redacts a secret's VALUE (not its name) anywhere it appears in a job log, replacing it with `***`. This is usually just noise-suppression, but it doubles as a diagnostic: if a field you print in plaintext - a config constant, a derived value, a computed string - comes out as `***`, that field is byte-for-byte equal to one of your secrets.

Real example: a guard printed `config {config.LICENSE_PUBKEY}` and it rendered as `config ***`. Since LICENSE_PUBKEY is a committed public key (not itself a secret), the only way it could be masked is if a SECRET held that same value - proving the operator had pasted the PUBLIC key into the secret meant to hold the PRIVATE key. The bug was in the secret's contents, not the code.

Corollary: you cannot exfiltrate a secret by re-printing it (it re-masks), and you can use this masking as a free equality check against secret values during debugging.

Related: [[3 Resources/Infra/CI-CD/GitHub Actions 'secret is not set' usually means a name mismatch - verify with gh secret list]], [[Signed-token verify fails on rotation drift - pinned public key and signing private key from different keypairs]]

%% ai-graph-start %%

**Related notes:**
- [[GitHub Actions 'secret is not set' usually means a name mismatch - verify with gh secret list]]
- [[Deliver a CI-minted credential via a masked short-retention artifact, not the run log]]
- [[secrets context is not available in GitHub Actions if conditions]]
- [[GitHub Actions gives fallback defaults because empty string is falsy in expressions]]
- [[Pipe a GCP service-account key straight into a GitHub secret without leaking it]]

%% ai-graph-end %%