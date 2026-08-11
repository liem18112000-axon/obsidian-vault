---
ai_hash: 56baff83ba98d18b
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-04
entities: []
source: session 2026-07-04, vinnstack silent approve no-op
status: seedling
tags:
- idempotency
- persistence
- migration
- vinnstack
title: Idempotency guards keyed on object presence break when hydration materializes
  the object
type: lesson
---

# Idempotency guards keyed on object presence break when hydration materializes the object

A guard like `if (rec.prd.jira) return rec;` ("already pushed to Jira - skip") encodes state in the mere PRESENCE of an optional object. That contract is fragile: when a storage rewrite (file JSON -> SQL hydration) started ALWAYS materializing `jira: {commented:false, storyKeys:[]}` for every record, the guard became permanently true - every approve action returned success in 3 seconds having written NOTHING, with no error anywhere. The user's only symptom: "I approved but nothing appears in Jira."

Rules:
- Idempotency/already-done guards must key on EVIDENCE (`status === "approved"`, `jira.commented`, `storyKeys.length > 0`), never on the shape/presence of a container object.
- Hydration layers must preserve optionality: only materialize an optional aggregate field when there is real data for it, because `field?:` presence IS semantics to some consumer.
- Diagnostic tell: a long-running operation "succeeding" in seconds = an early-return guard fired; check what the guard keys on before suspecting the operation itself.

## Related

- [[CREATE TABLE IF NOT EXISTS never upgrades existing tables - pair new columns with ALTER IF NOT EXISTS]]

%% ai-graph-start %%

**Related notes:**
- [[A server-side action rejection is a stale-view signal - resync on error]]
- [[Mirror form values by the SOURCE's real field keys, not assumed canonical names]]
- [[Per-feature migration scripts leave new tables silently missing until run]]
- [[Whole-aggregate read-modify-write for a per-child toggle causes lost updates under concurrent sibling writes]]
- [[Secondary-write failures should fail loud when their silent version was the actual bug]]

%% ai-graph-end %%