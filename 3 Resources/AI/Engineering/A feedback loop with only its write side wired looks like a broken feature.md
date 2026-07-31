---
title: "A feedback loop with only its write side wired looks like a broken feature"
created: 2026-07-22
type: lesson
status: seedling
source: "session 2026-07-22"
tags: [feedback-loop, prompt, vinnstack, gotcha]
---

# A feedback loop with only its write side wired looks like a broken feature

Vinnstack's track-comments bug (found 2026-07-22): inline comments on the business/technical question sets were fully wired on the WRITE side — UI panel, API, storage, even post-regenerate re-anchoring — but the regeneration prompt never READ them back (`generateInterrogation` built its prompt without `listComments`). To the user this presents as "the comment feature doesn't work": comments save fine, but regenerating ignores them, and after regen most go orphaned (re-anchoring against a replaced question set), which looks like they were silently discarded.

General lesson: a feedback loop has a write side (capture the feedback) and a read side (act on it in the next iteration). Wiring only the write side creates a convincing illusion of a working feature — every UI interaction succeeds — while the loop is actually open. When auditing a feedback feature, trace the CONSUMER: find where the stored feedback re-enters a prompt/decision, not just where it's saved.

Fix shape: inject `buildInlineCommentsBlock(comments, dir, docLabel)` into the track regen prompt (the same block the PRD and process-flow regens already used), with a docLabel param so the instruction text names the right artifact.

## Related

- [[3 Resources/AI/Engineering/Tier LLM effort per pipeline stage - pay where quality compounds, cut where the task is bounded]]
