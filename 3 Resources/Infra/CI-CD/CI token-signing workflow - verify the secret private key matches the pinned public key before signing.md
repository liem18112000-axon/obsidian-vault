---
ai_hash: 7c6753f2a8cf86a3
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-04
entities: []
source: session 2026-07-04 fb-info-project sign-license.yml
status: seedling
tags:
- github-actions
- licensing
- ci
- security
title: CI token-signing workflow - verify the secret private key matches the pinned
  public key before signing
type: lesson
---

# CI token-signing workflow - verify the secret private key matches the pinned public key before signing

A CI workflow that signs offline license tokens holds the private signing key as a repo secret (e.g. LICENSE_PRIVKEY). Before signing, it should PROVE that secret is the private half of the public key currently pinned in the shipped code (config.LICENSE_PUBKEY): derive the pubkey from the secret and fail the run on mismatch. Without this guard a stale/wrong secret silently mints tokens that no distributed build can verify - a failure you only discover on the end user's machine.

Cheap to implement: `publickey(bytes.fromhex(priv)).hex() == config.LICENSE_PUBKEY`, else exit with `::error::`. Also fail fast if either side is empty (empty pubkey = licensing off; empty secret = not configured).

Related: [[Offline license verification - the trust anchor must be baked into the binary, never runtime-configurable]], [[Offline token licensing - one operator keypair, tokens tracked in an auto-appended issuance ledger]]

%% ai-graph-start %%

**Related notes:**
- [[Offline token licensing - one operator keypair, tokens tracked in an auto-appended issuance ledger]]
- [[Signed-token verify fails on rotation drift - pinned public key and signing private key from different keypairs]]
- [[Offline license verification - the trust anchor must be baked into the binary, never runtime-configurable]]
- [[Key-fingerprint license id identifies the signing key, not the individual token]]
- [[Offline signed-token licensing for distributed binaries]]

%% ai-graph-end %%