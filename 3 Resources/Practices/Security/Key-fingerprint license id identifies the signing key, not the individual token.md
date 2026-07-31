---
ai_hash: 0432923c6d1515b6
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-04
entities: []
source: session 2026-07-04 fb-info-project sign-license.yml
status: seedling
tags:
- licensing
- design-decision
- identifiers
title: Key-fingerprint license id identifies the signing key, not the individual token
type: concept
---

# Key-fingerprint license id identifies the signing key, not the individual token

Deriving a license id from a fingerprint of the signing public key (e.g. `"key-" + LICENSE_PUBKEY[:16]`) makes every issued token record WHICH key/build signed it - useful for spotting tokens minted by a rotated or wrong key. But it is deliberately NOT unique per token: every token that key signs carries the same id, so it cannot trace an individual issuance to one machine. That per-token traceability must come from another field (e.g. the user name, or a separate unique id).

Design fork to be explicit about: key-fingerprint id (answers "which key signed this?") vs unique-per-token id (answers "which issuance is on this machine?"). They are different questions; pick per requirement, or combine (`key-<fp>-<counter>`).

Related: [[Offline token licensing - one operator keypair, tokens tracked in an auto-appended issuance ledger]], [[CI token-signing workflow - verify the secret private key matches the pinned public key before signing]]

%% ai-graph-start %%

**Related notes:**
- [[Offline token licensing - one operator keypair, tokens tracked in an auto-appended issuance ledger]]
- [[CI token-signing workflow - verify the secret private key matches the pinned public key before signing]]
- [[Signed-token verify fails on rotation drift - pinned public key and signing private key from different keypairs]]
- [[Offline license verification - the trust anchor must be baked into the binary, never runtime-configurable]]
- [[Offline license tokens cannot be revoked - only expiry, so prefer short validity plus renewal]]

%% ai-graph-end %%