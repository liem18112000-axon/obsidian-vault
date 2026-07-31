---
title: "Leo CDP event observerId is the pushing tokenkey and event/save can split from profile/save identity"
created: 2026-06-30
type: observation
status: seedling
source: "session 2026-06-30; live dump"
tags: [leo-cdp, identity, gotcha, events]
---

# Leo CDP event observerId is the pushing tokenkey and event/save can split from profile/save identity

Two identity facts observed on the live Leo CDP Data Observer API:

1. **`observerId` on a tracking event = the `tokenkey` that pushed it.** Our `mobile_install` event came back with `observerId: 30snDokTeHwFK4p6oBnMBB` — exactly the write token's `tokenkey`. So events are attributed to the ingesting token/observer, which is how a read token (a different key, e.g. `default_access_key`) can still see events written by another key in the same workspace.

2. **`event/save` (targetUpdateEmail) did NOT merge into the `profile/save` (primaryEmail) profile.** `POST /api/profile/save updateByKey=primaryEmail` returned profile id `1sM9LB0RC8UXRvD0lA5UNm` (email set, no events). The event pushed via `CdpHttpSink` with `targetUpdateEmail=<same email>` instead landed on a SEPARATE, email-less visitor profile `3DmymIkokb6VcEB9irMdbO`. Identity resolution between the two endpoints is not guaranteed to converge on one profile by email — so do not assume an event sent by `targetUpdateEmail` attaches to the profile you created by `primaryEmail`.

Positive signal: that event profile's `eventStatistics` showed `mobile_install: 4`, matching the 4 times the demo push was run — confirming repeated pushes aggregate (counter increments) rather than duplicate. Related: [[Identity-keyed CDP API breaks content-hash idempotency]], [[Leo CDP profile/list ignores start and limit and embeds event data]].

## Related

- [[Identity-keyed CDP API breaks content-hash idempotency]]
- [[Leo CDP profile/list ignores start and limit and embeds event data]]
