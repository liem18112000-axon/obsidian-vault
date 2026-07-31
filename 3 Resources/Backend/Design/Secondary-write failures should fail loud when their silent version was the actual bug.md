---
ai_hash: 72b9f9314f983f1c
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-04
entities: []
source: session 2026-07-04, vinnstack per-account credential mirror
status: seedling
tags:
- error-handling
- ux
- persistence
- vinnstack
title: Secondary-write failures should fail loud when their silent version was the
  actual bug
type: decision
---

# Secondary-write failures should fail loud when their silent version was the actual bug

Default instinct is to make a SECONDARY write (mirror, cache-fill, analytics, denormalized copy) non-fatal: log the error, let the primary operation report success. That's usually right — the primary succeeded, don't punish the caller for a best-effort side write.

But INVERT it when the silent version of that failure was itself the bug you just spent hours chasing. In vinnstack, the credential save verified against the provider (primary) and then mirrored to the signed-in account row (secondary). The mirror was non-fatal, so when it failed the UI still said "saved" while account_credentials stayed empty — the exact "no row ever appears" symptom. Fix: flip the contract — on mirror failure, return {ok:false, message:"Verified, but saving to your account failed: …"} so the operator can retry. Verification succeeding is NOT enough when the whole feature is "it's saved to your account."

Rule of thumb: a secondary write may be non-fatal only if the user has another signal that it didn't happen. If the secondary write IS the durable state the feature promises, its failure is primary. And when you flip it, flip the test with it — a test asserting the old "non-fatal → still ok" contract will (correctly) fail, and that failing test is the reminder to update the contract deliberately, not silently.

## Related

- [[Seed-in-memory-but-persist-on-save leaves no row when a prior layer already shows connected]]

%% ai-graph-start %%

**Related notes:**
- [[Seed-in-memory-but-persist-on-save leaves no row when a prior layer already shows connected]]
- [[Idempotency guards keyed on object presence break when hydration materializes the object]]
- [[Per-account write silently skipped when the server cant resolve the session looks saved, isnt]]
- [[Whole-aggregate read-modify-write for a per-child toggle causes lost updates under concurrent sibling writes]]
- [[Classify stream failures on the server, not the client]]

%% ai-graph-end %%