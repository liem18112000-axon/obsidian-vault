---
title: "Offline license tokens cannot be revoked - only expiry, so prefer short validity plus renewal"
created: 2026-07-04
type: concept
status: seedling
source: "session 2026-07-04 fb-info-project licensing discussion"
tags: [security, licensing, revocation, design-tradeoff]
---

# Offline license tokens cannot be revoked - only expiry, so prefer short validity plus renewal

When license tokens are verified fully offline (client checks a signature against a baked-in public key, no server callback), there is no revocation mechanism: every token you have ever signed stays valid until its own expiry date, even after you re-issue, upgrade, or fall out with the user. The only lever the operator controls after issuance is time.

Consequences:
- Prefer **short validity + renewal** over long tokens for users you are unsure about; renewal is just signing again, and it is cheap.
- An upgrade or correction does not invalidate the old token - the user holds both until the old one expires. Price/limit downgrades mid-term are effectively impossible.
- If real revocation is a requirement, the scheme needs an online component (activation server, CRL-style denylist fetched periodically) - which trades away the offline property.

Related: [[Offline token licensing - one operator keypair, tokens tracked in an auto-appended issuance ledger]], [[Offline license verification - the trust anchor must be baked into the binary, never runtime-configurable]]
