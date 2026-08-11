---
ai_hash: e479e10e0a4acee5
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-22
entities: []
source: session 2026-07-22
status: seedling
tags:
- feedback-loop
- prompt
- vinnstack
- gotcha
title: A feedback loop with only its write side wired looks like a broken feature
type: lesson
---

# A feedback loop with only its write side wired looks like a broken feature

Vinnstack's track-comments bug (found 2026-07-22): inline comments on the business/technical question sets were fully wired on the WRITE side — UI panel, API, storage, even post-regenerate re-anchoring — but the regeneration prompt never READ them back (`generateInterrogation` built its prompt without `listComments`). To the user this presents as "the comment feature doesn't work": comments save fine, but regenerating ignores them, and after regen most go orphaned (re-anchoring against a replaced question set), which looks like they were silently discarded.

General lesson: a feedback loop has a write side (capture the feedback) and a read side (act on it in the next iteration). Wiring only the write side creates a convincing illusion of a working feature — every UI interaction succeeds — while the loop is actually open. When auditing a feedback feature, trace the CONSUMER: find where the stored feedback re-enters a prompt/decision, not just where it's saved.

Fix shape: inject `buildInlineCommentsBlock(comments, dir, docLabel)` into the track regen prompt (the same block the PRD and process-flow regens already used), with a docLabel param so the instruction text names the right artifact.

## Related

- [[3 Resources/AI/Engineering/Tier LLM effort per pipeline stage - pay where quality compounds, cut where the task is bounded]]

%% ai-graph-start %%

**Related notes:**
- [[Gesture-only features need an always-visible teacher - empty state must not hide the affordance]]
- [[PRD-parity checklist - what comment-driven regenerate with versions actually requires]]
- [[Inject anchored inline comments into LLM regeneration prompts as quoted passages]]
- [[Regenerate-from-review-feedback pattern reuse the branchPR, don't open a new one]]
- [[Async-enriched columns need a lazy backfill for pre-feature rows]]

%% ai-graph-end %%