---
title: "GitHub Actions masks a secret's VALUE everywhere - a plaintext field logging as *** means it equals a secret"
created: 2026-07-04
type: lesson
status: seedling
source: "session 2026-07-04 fb-info-project run 28708059025"
tags: [github-actions, secrets, debugging, gotcha]
---

# GitHub Actions masks a secret's VALUE everywhere - a plaintext field logging as *** means it equals a secret

GitHub Actions automatically redacts a secret's VALUE (not its name) anywhere it appears in a job log, replacing it with `***`. This is usually just noise-suppression, but it doubles as a diagnostic: if a field you print in plaintext - a config constant, a derived value, a computed string - comes out as `***`, that field is byte-for-byte equal to one of your secrets.

Real example: a guard printed `config {config.LICENSE_PUBKEY}` and it rendered as `config ***`. Since LICENSE_PUBKEY is a committed public key (not itself a secret), the only way it could be masked is if a SECRET held that same value - proving the operator had pasted the PUBLIC key into the secret meant to hold the PRIVATE key. The bug was in the secret's contents, not the code.

Corollary: you cannot exfiltrate a secret by re-printing it (it re-masks), and you can use this masking as a free equality check against secret values during debugging.

Related: [[GitHub Actions secret is not set usually means a name mismatch - verify with gh secret list]], [[Signed-token verify fails on rotation drift - pinned public key and signing private key from different keypairs]]
