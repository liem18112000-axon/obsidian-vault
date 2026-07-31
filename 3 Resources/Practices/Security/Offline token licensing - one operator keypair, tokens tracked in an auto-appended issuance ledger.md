---
title: "Offline token licensing - one operator keypair, tokens tracked in an auto-appended issuance ledger"
created: 2026-07-04
type: model
status: seedling
source: "session 2026-07-04 fb-info-project licensing discussion"
tags: [security, licensing, key-management, operations]
---

# Offline token licensing - one operator keypair, tokens tracked in an auto-appended issuance ledger

In a signed-token licensing scheme there is exactly one keypair, owned by the operator. Users never hold keys - each user gets a *token* (payload + signature) minted with the single private key. "Key management for all users" therefore collapses into two tasks:

1. **Guard the one private signing key.** Password manager or protected file outside the repo; it is never shipped, never committed. If it leaks, the recovery cost is full: new keypair, rebuild + redistribute the binary with the new public key, re-issue every active token.
2. **Keep an issuance ledger.** The signing step is the only moment the operator sees the token, so the signing tool should auto-append one record per mint (id, user, tier, issued, expires, limits, token) to a gitignored JSONL/CSV. Auto-append beats a manual log because it cannot drift from what was actually issued. The ledger answers "who is active", supports lost-token reissue by copy-paste, and is the re-issue list after a key rotation.

Give every token a unique `id` (not a shared default) - the id is embedded in the signed payload and surfaces in the client's local license state, so a machine can be traced back to its ledger row and a renewal is distinguishable from the original.

Related: [[Offline license verification - the trust anchor must be baked into the binary, never runtime-configurable]], [[Offline license tokens cannot be revoked - only expiry, so prefer short validity plus renewal]]
