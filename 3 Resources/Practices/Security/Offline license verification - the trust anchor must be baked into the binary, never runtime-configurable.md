---
ai_hash: 3a8585d44f345d8a
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-04
entities: []
source: session 2026-07-04 fb-info-project src/config.py
status: seedling
tags:
- security
- licensing
- ed25519
- design-decision
title: Offline license verification - the trust anchor must be baked into the binary,
  never runtime-configurable
type: concept
---

# Offline license verification - the trust anchor must be baked into the binary, never runtime-configurable

In an offline licensing scheme (signed tokens verified client-side), the verification public key is the trust anchor and must be a compile-time constant baked into the shipped binary - never read from an env var, config file, or any other user-writable channel. If the key is runtime-overridable, the end user can generate their own keypair, point the app at their own public key, sign themselves unlimited tokens, and the whole scheme collapses. Only the *token* is user-supplied; the key that judges it is not.

Concrete instance: fb-info-project pins `LICENSE_PUBKEY` as a hardcoded string in `src/config.py`, with a comment explicitly rejecting env-var override for this reason. A useful side convention there: empty key = licensing entirely OFF, so open-source/dev builds behave as if the feature does not exist - the operator pastes their key only when producing a licensed build.

(The user can still patch the binary itself, of course - this raises the bar from "set an env var" to "reverse-engineer the executable", which is the realistic goal of offline licensing.)

Related: [[Dual-mode CMD wrapper - args pass through, no args opens an interactive menu]]

%% ai-graph-start %%

**Related notes:**
- [[Offline signed-token licensing for distributed binaries]]
- [[CI token-signing workflow - verify the secret private key matches the pinned public key before signing]]
- [[Operator-only config can be externalized to a data file with no client rebuild]]
- [[Offline token licensing - one operator keypair, tokens tracked in an auto-appended issuance ledger]]
- [[Key-fingerprint license id identifies the signing key, not the individual token]]

%% ai-graph-end %%