---
ai_hash: 71b05f79abbc159a
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-26
entities: []
source: session 2026-06-26
status: seedling
tags:
- appsflyer
- pull-api
- s2s
- gotcha
- leo-cdp
title: AppsFlyer appsflyer_id is minted at install — fabricated IDs can't round-trip
  through Pull API
type: lesson
---

# AppsFlyer appsflyer_id is minted at install — fabricated IDs can't round-trip through Pull API

An AppsFlyer `appsflyer_id` is **issued by AppsFlyer itself** at first app launch (via the SDK or a genuine attribution event) — it is not a value an external caller can invent. The raw-data export behind the Pull API only contains events that hang off a **real install record**, so an S2S in-app event referencing a fabricated `appsflyer_id` has no install to attach to and is **silently dropped**: accepted at the edge (HTTP 2xx) but never exportable.

This is why the connector's synthetic generator (which builds IDs with `_af_id()` like `1234567890123-1234567890123`) produces **100 events sent → 0 rows pulled**: the send succeeds, the events just never become real data. Confirmed again on 2026-06-25 for app `com.leocdp`.

Design consequence: the generator is the *offline* counterpart to the Pull connector by design (DESIGN_GENERATOR.md §1) — `--out csv`/`--out cdp` are the intended test paths because they never depend on AppsFlyer accepting the data. The S2S mode is only meaningful against a **real** `appsflyer_id`.

See [[How to get real AppsFlyer Pull API data with the synthetic generator]] for the workaround.

## Related

- [[How to get real AppsFlyer Pull API data with the synthetic generator]]

%% ai-graph-start %%

**Related notes:**
- [[How to get real AppsFlyer Pull API data with the synthetic generator]]
- [[AppsFlyer only attributes events to installs recorded under the same app_id]]
- [[AppsFlyer Push API is the inverse of the Pull API]]
- [[Identity-keyed CDP API breaks content-hash idempotency]]
- [[AppsFlyer raw-data Pull API is plan-gated - 400 subscription error]]

%% ai-graph-end %%