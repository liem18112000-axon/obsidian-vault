---
ai_hash: 99c29b944c240372
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-05
entities: []
source: vinnstack code review 2026-07-05, approveStoryFlow
status: seedling
tags:
- audit
- pii
- git
- convention
- vinnstack
title: Keep audit updatedBy a role token, not a user identity, when it feeds commit
  messages or mirrors
type: gotcha
---

# Keep audit updatedBy a role token, not a user identity, when it feeds commit messages or mirrors

If an aggregate carries a free-text "who touched this" field (updatedBy / lastModifiedBy) whose established vocabulary is a small set of ROLE/ACTOR TOKENS (e.g. "human", "epic-to-prd", "story-to-process-flow"), do NOT set it to a person's identity (email/name) in one new mutator. Two costs:

1. PII leak into unexpected sinks. That field often flows verbatim into a git commit message (autoCommit) and a browsable mirror file — so a raw operator email lands in git history and a synced Obsidian/vault doc. Once committed it's effectively permanent and may be indexed.
2. Convention break. Any consumer keyed on the known role tokens (log parser, commit-message grep, state machine) is surprised by an out-of-vocabulary value.

Capture the person's identity in a STRUCTURED column meant for it (approved_by, actor_id) and keep the audit token a role. The structured column is queryable; the free-text line stays parseable. General rule: identity belongs in a typed field, not in a human-readable audit string that has downstream string consumers.

## Related

- [[Whole-aggregate read-modify-write for a per-child toggle causes lost updates under concurrent sibling writes]]

%% ai-graph-start %%

**Related notes:**
- [[Whole-aggregate read-modify-write for a per-child toggle causes lost updates under concurrent sibling writes]]
- [[Mirror form values by the SOURCE's real field keys, not assumed canonical names]]
- [[Idempotency guards keyed on object presence break when hydration materializes the object]]
- [[Block AI commit attribution by anchoring on the email, not the trailer label]]

%% ai-graph-end %%