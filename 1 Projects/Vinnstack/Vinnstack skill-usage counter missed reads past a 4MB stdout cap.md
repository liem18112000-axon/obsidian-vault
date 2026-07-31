---
title: "Vinnstack skill-usage counter missed reads past a 4MB stdout cap"
created: 2026-07-08
type: lesson
status: seedling
source: "vinnstack session, 2026-07-08"
tags: [vinnstack, bug, stream-json, skills]
---

# Vinnstack skill-usage counter missed reads past a 4MB stdout cap

Vinnstack'\''s skill-usage counter (the "↻ N" badge in the Skills view, meant to show how many times the agent actually Read a given SKILL.md) was silently broken by its detection method: it accumulated a chat run'\''s entire raw stdout into an in-memory string capped at 4MB, then at the end of the run did a plain substring search for each known skill'\''s absolute file path.

Two compounding problems: once accumulated stdout crossed the 4MB cap, ALL further output was silently dropped forever for that run (not evicted-and-refilled, just stopped) — and vinnstack'\''s "ultracode" mode runs Claude Code at `--effort xhigh` with `--include-partial-messages` (verbose token-by-token streaming), which can produce many MB of stream-json in one non-trivial run, so a SKILL.md read anywhere after the cap was invisible. Separately, the whole thing was a heuristic raw-text substring match wrapped in a try/catch that swallowed any real error silently, so nobody would ever see it fail.

Fix: parse each stream-json line as JSON as it arrives, and for every complete `{"type":"assistant","message":{"content":[...]}}` event, scan `content` for blocks where `type === "tool_use"`, `name === "Read"`, and `input.file_path` is a string — record each one immediately. No accumulation buffer, no size cap, no substring heuristic. See [[Parse structured tool_use blocks in a stream-json transcript, never substring-scan raw text]] for the general lesson this generalizes to.

## Related
[[Dead bundling config outlives the runtime code that read it]]
