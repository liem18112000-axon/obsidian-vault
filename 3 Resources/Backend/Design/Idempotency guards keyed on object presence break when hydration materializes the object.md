---
title: "Idempotency guards keyed on object presence break when hydration materializes the object"
created: 2026-07-04
type: lesson
status: seedling
source: "session 2026-07-04, vinnstack silent approve no-op"
tags: [idempotency, persistence, migration, vinnstack]
---

# Idempotency guards keyed on object presence break when hydration materializes the object

A guard like `if (rec.prd.jira) return rec;` ("already pushed to Jira - skip") encodes state in the mere PRESENCE of an optional object. That contract is fragile: when a storage rewrite (file JSON -> SQL hydration) started ALWAYS materializing `jira: {commented:false, storyKeys:[]}` for every record, the guard became permanently true - every approve action returned success in 3 seconds having written NOTHING, with no error anywhere. The user's only symptom: "I approved but nothing appears in Jira."

Rules:
- Idempotency/already-done guards must key on EVIDENCE (`status === "approved"`, `jira.commented`, `storyKeys.length > 0`), never on the shape/presence of a container object.
- Hydration layers must preserve optionality: only materialize an optional aggregate field when there is real data for it, because `field?:` presence IS semantics to some consumer.
- Diagnostic tell: a long-running operation "succeeding" in seconds = an early-return guard fired; check what the guard keys on before suspecting the operation itself.

## Related

- [[CREATE TABLE IF NOT EXISTS never upgrades existing tables - pair new columns with ALTER IF NOT EXISTS]]
