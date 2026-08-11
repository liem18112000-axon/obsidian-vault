---
ai_hash: 1dfd36aac0a5a8e5
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-30
entities:
- Leo CDP
- observerId
- tokenkey
- event/save API
- profile/save API
- tracking event
- mobile_install event
- write token
- read token
- default_access_key
- workspace
- targetUpdateEmail
- primaryEmail
- CdpHttpSink
- visitor profile
- event profile
- eventStatistics
- Identity-keyed CDP API breaks content-hash idempotency
- Leo CDP profilelist ignores start and limit and embeds event data
- Leo CDP Data Observer API
- profile ID 1sM9LB0RC8UXRvD0lA5UNm
- profile ID 3DmymIkokb6VcEB9irMdbO
- identity resolution
- repeated pushes
- counter increments
- duplicate events
- events
- ingesting token/observer
- identity facts
source: session 2026-06-30; live dump
status: seedling
tags:
- leo-cdp
- identity
- gotcha
- events
title: Leo CDP event observerId is the pushing tokenkey and event/save can split from
  profile/save identity
type: observation
---

# Leo CDP event observerId is the pushing tokenkey and event/save can split from profile/save identity

Two identity facts observed on the live Leo CDP Data Observer API:

1. **`observerId` on a tracking event = the `tokenkey` that pushed it.** Our `mobile_install` event came back with `observerId: 30snDokTeHwFK4p6oBnMBB` — exactly the write token's `tokenkey`. So events are attributed to the ingesting token/observer, which is how a read token (a different key, e.g. `default_access_key`) can still see events written by another key in the same workspace.

2. **`event/save` (targetUpdateEmail) did NOT merge into the `profile/save` (primaryEmail) profile.** `POST /api/profile/save updateByKey=primaryEmail` returned profile id `1sM9LB0RC8UXRvD0lA5UNm` (email set, no events). The event pushed via `CdpHttpSink` with `targetUpdateEmail=<same email>` instead landed on a SEPARATE, email-less visitor profile `3DmymIkokb6VcEB9irMdbO`. Identity resolution between the two endpoints is not guaranteed to converge on one profile by email — so do not assume an event sent by `targetUpdateEmail` attaches to the profile you created by `primaryEmail`.

Positive signal: that event profile's `eventStatistics` showed `mobile_install: 4`, matching the 4 times the demo push was run — confirming repeated pushes aggregate (counter increments) rather than duplicate. Related: [[Identity-keyed CDP API breaks content-hash idempotency]], [[1 Projects/appsflyer-connector/Leo CDP profilelist ignores start and limit and embeds event data]].

## Related

- [[Identity-keyed CDP API breaks content-hash idempotency]]
- [[1 Projects/appsflyer-connector/Leo CDP profilelist ignores start and limit and embeds event data]]

%% ai-graph-start %%

**Related notes:**
- [[Identity-keyed CDP API breaks content-hash idempotency]]
- [[Leo CDP save returns 200 but eventlist cannot read it back]]
- [[Leo CDP public REST API contract]]
- [[Leo CDP profilelist ignores start and limit and embeds event data]]
- [[Leo CDP admin dashboard is a hash-routed SPA on a separate host from the API]]

**Relations:**
- observerId — *is* — tokenkey
- event/save API — *can_split_from* — profile/save API
- observerId — *is_on* — tracking event
- tokenkey — *pushed* — tracking event
- mobile_install event — *has_observerId* — tokenkey
- tokenkey — *belongs_to* — write token
- events — *attributed_to* — ingesting token/observer
- write token — *is_a_type_of* — ingesting token/observer
- read token — *is_a_different_key_from* — write token
- default_access_key — *is_an_example_of* — read token
- read token — *can_see* — events
- events — *written_by* — write token
- read token — *operates_in* — workspace
- write token — *operates_in* — workspace
- event/save API — *did_not_merge_into* — profile/save API
- event/save API — *uses_identity_field* — targetUpdateEmail
- profile/save API — *uses_identity_field* — primaryEmail
- profile/save API — *returned* — profile ID 1sM9LB0RC8UXRvD0lA5UNm
- profile ID 1sM9LB0RC8UXRvD0lA5UNm — *has_status* — email set
- profile ID 1sM9LB0RC8UXRvD0lA5UNm — *has_status* — no events
- CdpHttpSink — *pushed* — events
- events — *landed_on* — profile ID 3DmymIkokb6VcEB9irMdbO
- profile ID 3DmymIkokb6VcEB9irMdbO — *is_a_type_of* — visitor profile
- profile ID 3DmymIkokb6VcEB9irMdbO — *is* — email-less
- identity resolution — *is_not_guaranteed_between* — event/save API
- identity resolution — *is_not_guaranteed_between* — profile/save API
- event profile — *has* — eventStatistics
- eventStatistics — *shows_count_for* — mobile_install event
- eventStatistics — *shows_value* — 4
- repeated pushes — *result_in* — counter increments
- repeated pushes — *do_not_result_in* — duplicate events
- Leo CDP — *is_related_to* — Identity-keyed CDP API breaks content-hash idempotency
- Leo CDP — *is_related_to* — Leo CDP profilelist ignores start and limit and embeds event data
- Leo CDP Data Observer API — *provides* — identity facts
- Leo CDP Data Observer API — *is_part_of* — Leo CDP
- Leo CDP — *has_API* — event/save API
- Leo CDP — *has_API* — profile/save API
- Leo CDP — *has_API* — Leo CDP Data Observer API

%% ai-graph-end %%