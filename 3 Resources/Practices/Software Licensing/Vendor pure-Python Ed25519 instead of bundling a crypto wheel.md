---
ai_hash: 3396c501f01d9818
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-14
entities: []
source: fb-info-project session 2026-06-14
status: seedling
tags:
- ed25519
- pyinstaller
- packaging
- cryptography
title: Vendor pure-Python Ed25519 instead of bundling a crypto wheel
type: lesson
---

# Vendor pure-Python Ed25519 instead of bundling a crypto wheel

When a binary only needs to **verify one signature per run** (not per request), a vendored pure-Python RFC 8032 Ed25519 implementation (stdlib `hashlib` only) is the better choice over `cryptography` or `pynacl`.

**Why:** the reference implementation verifies in ~1.3 s, which is irrelevant when the surrounding operation (e.g. a scrape) takes minutes. In exchange you add **zero new wheels** to the PyInstaller bundle — no extra `collect_all`, no native-DLL bundling, no packaging surprises.

**Caveat:** the reference impl is NOT constant-time, so only use it where the secret is not attacker-controlled — e.g. verifying the operator's signature with a public key, or signing on the operator's own machine. Never as a timing oracle a third party can probe.

Same "stdlib-only to keep the bundle clean" trade-off applies to any optional feature in a packaged app. Related: [[Offline signed-token licensing for distributed binaries]].

## Related

- [[Offline signed-token licensing for distributed binaries]]

%% ai-graph-start %%

**Related notes:**
- [[Offline signed-token licensing for distributed binaries]]
- [[Offline license verification - the trust anchor must be baked into the binary, never runtime-configurable]]
- [[CI token-signing workflow - verify the secret private key matches the pinned public key before signing]]
- [[Offline token licensing - one operator keypair, tokens tracked in an auto-appended issuance ledger]]

%% ai-graph-end %%