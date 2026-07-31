---
ai_hash: 4b2b69a1b6430d29
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-14
entities: []
source: fb-info-project session 2026-06-14
status: seedling
tags:
- licensing
- cryptography
- ed25519
- distribution
- security
title: Offline signed-token licensing for distributed binaries
type: concept
---

# Offline signed-token licensing for distributed binaries

Software running on the end user's machine with no server (e.g. a PyInstaller exe) can still enforce access control with an **Ed25519-signed token**. The operator holds the PRIVATE key and signs a tiny payload (`{user, expires, limits}`); the binary embeds only the PUBLIC key and verifies **offline**. Without the private key a user can neither forge a token nor extend its expiry.

Token shape that works well: `base64url(payload_json) + "." + base64url(signature)` — one line in a file dropped next to the app.

This is the hub note for the scheme; the load-bearing details live in their own notes:
- Trust anchor must be a compiled-in constant, never env/config overridable → [[Offline license verification - the trust anchor must be baked into the binary, never runtime-configurable]]
- Expiry is enforceable, usage counters are not → [[Offline licensing expiry is strong, usage counters only best-effort]]
- No revocation exists, so prefer short validity + renewal → [[Offline license tokens cannot be revoked - only expiry, so prefer short validity plus renewal]]
- One operator keypair + an auto-appended issuance ledger → [[Offline token licensing - one operator keypair, tokens tracked in an auto-appended issuance ledger]]
- Verify with a vendored pure-Python impl, not a crypto wheel → [[Vendor pure-Python Ed25519 instead of bundling a crypto wheel]]

%% ai-graph-start %%

**Related notes:**
- [[Offline token licensing - one operator keypair, tokens tracked in an auto-appended issuance ledger]]
- [[Offline license verification - the trust anchor must be baked into the binary, never runtime-configurable]]
- [[Vendor pure-Python Ed25519 instead of bundling a crypto wheel]]
- [[CI token-signing workflow - verify the secret private key matches the pinned public key before signing]]
- [[Offline license tokens cannot be revoked - only expiry, so prefer short validity plus renewal]]

%% ai-graph-end %%