---
ai_hash: feee2c01c541de7d
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-04
entities: []
source: session 2026-07-04 fb-info-project run 28699948285
status: seedling
tags:
- cryptography
- keypair
- licensing
- debugging
title: Signed-token verify fails on rotation drift - pinned public key and signing
  private key from different keypairs
type: lesson
---

# Signed-token verify fails on rotation drift - pinned public key and signing private key from different keypairs

A signed-token scheme can fail even when both a private signing key and a public verify key are correctly present - if they are halves of DIFFERENT keypairs (rotation drift). Symptom from a pre-sign guard: "derived <pubA> != config <pubB>", where deriving the public key from the secret private key yields pubA but the code/config pins pubB.

Cause: someone ran keygen more than once and mixed the outputs - e.g. stored the private key from run 2 in the secret but left the public key from run 1 pinned in config. Each keygen output is an inseparable pair; you must deploy the public half and store the private half FROM THE SAME RUN.

Fix = make them match: either update the pinned public key to the private key's true public half, or set the secret to the private key whose public half is already pinned. Cleanest when a key has leaked: rotate - one fresh keygen, pin its public half, store its private half, discard the old pair.

Related: [[CI token-signing workflow - verify the secret private key matches the pinned public key before signing]], [[Offline license verification - the trust anchor must be baked into the binary, never runtime-configurable]]

%% ai-graph-start %%

**Related notes:**
- [[CI token-signing workflow - verify the secret private key matches the pinned public key before signing]]
- [[Key-fingerprint license id identifies the signing key, not the individual token]]
- [[Offline token licensing - one operator keypair, tokens tracked in an auto-appended issuance ledger]]
- [[Offline license verification - the trust anchor must be baked into the binary, never runtime-configurable]]
- [[Offline license tokens cannot be revoked - only expiry, so prefer short validity plus renewal]]

%% ai-graph-end %%