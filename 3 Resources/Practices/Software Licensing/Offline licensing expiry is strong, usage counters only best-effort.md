---
ai_hash: d8e9925b37bdd995
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-14
entities: []
source: fb-info-project session 2026-06-14
status: seedling
tags:
- licensing
- security
- offline
- gotcha
title: 'Offline licensing: expiry is strong, usage counters only best-effort'
type: lesson
---

# Offline licensing: expiry is strong, usage counters only best-effort

In an offline licensing scheme, not all limits are equally enforceable:

- **Expiry — STRONG.** It is signed into the token, so it cannot be extended without the operator's private key. This is the guarantee you can actually lean on.
- **Usage quotas (run counts, volume) — BEST-EFFORT.** They are tracked in a local state file. An HMAC tag over the counters resets them on mismatch, and **deleting** the file resets usage to zero. You cannot prevent either offline.

> [!warning] An HMAC tag is only *corruption detection*, not *tamper-evidence*, when its key is not secret. If you derive the key from the embedded public key (or anything else shipped in the client), a determined user can read that value, recompute the tag, and forge the counters — so it catches *accidental* corruption only. Calling it "tamper-evident" overstates the guarantee. Real tamper-evidence needs a key the client never sees, i.e. a server.

This asymmetry is inherent to *any* offline scheme: nothing stops a user from wiping or forging local state. Only an **online check-in** makes usage caps truly hard (the server holds the count and the secret). Design accordingly — make the time bound your primary lever and treat usage caps as friction, not a wall.

Part of [[Offline signed-token licensing for distributed binaries]].

## Related

- [[Offline signed-token licensing for distributed binaries]]

%% ai-graph-start %%

**Related notes:**
- [[Offline license tokens cannot be revoked - only expiry, so prefer short validity plus renewal]]
- [[Offline token licensing - one operator keypair, tokens tracked in an auto-appended issuance ledger]]
- [[Offline signed-token licensing for distributed binaries]]
- [[Offline license verification - the trust anchor must be baked into the binary, never runtime-configurable]]
- [[CI token-signing workflow - verify the secret private key matches the pinned public key before signing]]

%% ai-graph-end %%