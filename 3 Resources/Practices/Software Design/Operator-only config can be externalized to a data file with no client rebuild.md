---
ai_hash: 8d143dc6c00be3c7
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-04
entities: []
source: session 2026-07-04 fb-info-project tiers.json
status: seedling
tags:
- design
- configuration
- licensing
title: Operator-only config can be externalized to a data file with no client rebuild
type: concept
---

# Operator-only config can be externalized to a data file with no client rebuild

When a piece of configuration lives entirely on ONE side of a trust/interface boundary, it can be externalized to a data file freely - no rebuild or coordination with the other side. In fb-info-project, license "tiers" are purely operator-side: `sign` expands a tier into numeric limits baked into the signed token, and the client verifier only ever reads those numbers, never the tier name. So moving TIERS from a Python dict into tools/tiers.json changes nothing the shipped exe sees - adding/tuning tiers needs no client rebuild, no token-format change, no re-verification.

The test for "is this safe to externalize?": does the value cross the interface (get shipped/serialized to the other party)? If NO, a data file is strictly better than a code literal. If YES (e.g. the public key baked into the exe), it is part of the contract and cannot be casually externalized.

Caveat: a parallel hardcoded list elsewhere does not auto-follow the file - here the GitHub `workflow_dispatch` tier dropdown is a separate literal that still needs a manual edit when a tier is added.

Related: [[Offline token licensing - one operator keypair, tokens tracked in an auto-appended issuance ledger]]

%% ai-graph-start %%

**Related notes:**
- [[Offline license verification - the trust anchor must be baked into the binary, never runtime-configurable]]
- [[Offline signed-token licensing for distributed binaries]]
- [[fb-info-project CI and build workflow split]]
- [[Offline token licensing - one operator keypair, tokens tracked in an auto-appended issuance ledger]]
- [[Offline licensing expiry is strong, usage counters only best-effort]]

%% ai-graph-end %%