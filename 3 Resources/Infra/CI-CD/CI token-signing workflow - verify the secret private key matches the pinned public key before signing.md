---
title: "CI token-signing workflow - verify the secret private key matches the pinned public key before signing"
created: 2026-07-04
type: lesson
status: seedling
source: "session 2026-07-04 fb-info-project sign-license.yml"
tags: [github-actions, licensing, ci, security]
---

# CI token-signing workflow - verify the secret private key matches the pinned public key before signing

A CI workflow that signs offline license tokens holds the private signing key as a repo secret (e.g. LICENSE_PRIVKEY). Before signing, it should PROVE that secret is the private half of the public key currently pinned in the shipped code (config.LICENSE_PUBKEY): derive the pubkey from the secret and fail the run on mismatch. Without this guard a stale/wrong secret silently mints tokens that no distributed build can verify - a failure you only discover on the end user's machine.

Cheap to implement: `publickey(bytes.fromhex(priv)).hex() == config.LICENSE_PUBKEY`, else exit with `::error::`. Also fail fast if either side is empty (empty pubkey = licensing off; empty secret = not configured).

Related: [[Offline license verification - the trust anchor must be baked into the binary, never runtime-configurable]], [[Offline token licensing - one operator keypair, tokens tracked in an auto-appended issuance ledger]]
