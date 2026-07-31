---
title: "Operator-only config can be externalized to a data file with no client rebuild"
created: 2026-07-04
type: concept
status: seedling
source: "session 2026-07-04 fb-info-project tiers.json"
tags: [design, configuration, licensing]
---

# Operator-only config can be externalized to a data file with no client rebuild

When a piece of configuration lives entirely on ONE side of a trust/interface boundary, it can be externalized to a data file freely - no rebuild or coordination with the other side. In fb-info-project, license "tiers" are purely operator-side: `sign` expands a tier into numeric limits baked into the signed token, and the client verifier only ever reads those numbers, never the tier name. So moving TIERS from a Python dict into tools/tiers.json changes nothing the shipped exe sees - adding/tuning tiers needs no client rebuild, no token-format change, no re-verification.

The test for "is this safe to externalize?": does the value cross the interface (get shipped/serialized to the other party)? If NO, a data file is strictly better than a code literal. If YES (e.g. the public key baked into the exe), it is part of the contract and cannot be casually externalized.

Caveat: a parallel hardcoded list elsewhere does not auto-follow the file - here the GitHub `workflow_dispatch` tier dropdown is a separate literal that still needs a manual edit when a tier is added.

Related: [[Offline token licensing - one operator keypair, tokens tracked in an auto-appended issuance ledger]]
