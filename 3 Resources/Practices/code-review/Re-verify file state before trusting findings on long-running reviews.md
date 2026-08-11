---
ai_hash: 39716fd8d0844ae8
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-10
entities: []
source: luz_docs parallelize code review, 2026-07-09/10
status: seedling
tags:
- code-review
- agents
- workflow
- git
- gotcha
title: Re-verify file state before trusting findings on long-running reviews
type: lesson
---

# Re-verify file state before trusting findings on long-running reviews

When a code review dispatches long-running background subagents (minutes to over an hour), the target branch can change underneath the review — especially on a live/shared repo where a teammate (or the same user, in another window) commits a fix mid-review. Treat any sub-agent finding that contradicts your own earlier direct reading of a file as a signal to re-check reality, not as an error to dismiss.

Concretely: after reading a file's full contents at the start of a review, a much-later sub-agent (in a Phase-3 gap sweep) reported a method call that did not exist in the version just read. Rather than assuming the sub-agent hallucinated, re-reading the file directly showed the method now existed — `git log`/`git status` on that exact file confirmed a real, new commit had landed (from the same user, timestamped during the review's run) that added it, and the commit fixed exactly the top bug the review had flagged.

**Practical workflow rule:** before finalizing a report built from long-running parallel/background agent work, re-verify any claim that conflicts with your own earlier reads by (1) grepping for the specific symbol/line the sub-agent cited, (2) checking `git status`/`git diff` for uncommitted changes, and (3) checking `git log` on the specific file(s) for new commits landed during the session. This distinguishes 'the sub-agent is wrong' from 'the ground truth moved,' and in the latter case the review's conclusions need updating, not just the wording.

## Related

- [[luz_docs stamps _shard on create to keep sharding gate stable]]

%% ai-graph-start %%

**Related notes:**
- [[A refactor that removes a method must grep tests for its name before merging]]
- [[Gate behavior changes must update tests asserting old fallthrough in the same commit]]
- [[Re-check live dependencies right before committing in a shared repo]]
- [[Branch created from current HEAD drags unrelated commits — verify against originmaster]]
- [[A concurrent session's git stash can silently revert your in-progress edits]]

%% ai-graph-end %%