---
ai_hash: 4d1cc6c928707698
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-08
entities:
- Vinnstack
- skill-usage counter
- 4MB stdout cap
- Skills view
- SKILL.md
- detection method
- raw stdout
- in-memory string
- substring search
- absolute file path
- ultracode mode
- Claude Code
- '`--effort xhigh`'
- '`--include-partial-messages`'
- stream-json
- heuristic raw-text substring match
- try/catch
- JSON parsing
- tool_use blocks
- Read tool
- '`input.file_path`'
- Parse structured tool_use blocks in a stream-json transcript, never substring-scan
  raw text
- Dead bundling config outlives the runtime code that read it
source: vinnstack session, 2026-07-08
status: seedling
tags:
- vinnstack
- bug
- stream-json
- skills
title: Vinnstack skill-usage counter missed reads past a 4MB stdout cap
type: lesson
---

# Vinnstack skill-usage counter missed reads past a 4MB stdout cap

Vinnstack'\''s skill-usage counter (the "↻ N" badge in the Skills view, meant to show how many times the agent actually Read a given SKILL.md) was silently broken by its detection method: it accumulated a chat run'\''s entire raw stdout into an in-memory string capped at 4MB, then at the end of the run did a plain substring search for each known skill'\''s absolute file path.

Two compounding problems: once accumulated stdout crossed the 4MB cap, ALL further output was silently dropped forever for that run (not evicted-and-refilled, just stopped) — and vinnstack'\''s "ultracode" mode runs Claude Code at `--effort xhigh` with `--include-partial-messages` (verbose token-by-token streaming), which can produce many MB of stream-json in one non-trivial run, so a SKILL.md read anywhere after the cap was invisible. Separately, the whole thing was a heuristic raw-text substring match wrapped in a try/catch that swallowed any real error silently, so nobody would ever see it fail.

Fix: parse each stream-json line as JSON as it arrives, and for every complete `{"type":"assistant","message":{"content":[...]}}` event, scan `content` for blocks where `type === "tool_use"`, `name === "Read"`, and `input.file_path` is a string — record each one immediately. No accumulation buffer, no size cap, no substring heuristic. See [[Parse structured tool_use blocks in a stream-json transcript, never substring-scan raw text]] for the general lesson this generalizes to.

## Related
[[Dead bundling config outlives the runtime code that read it]]

%% ai-graph-start %%

**Related notes:**
- [[Parse structured tool_use blocks in a stream-json transcript, never substring-scan raw text]]
- [[Classify stream failures on the server, not the client]]
- [[Keep errorfailure text out of LLM-summarized durable memory]]
- [[Unrestricted directory mounts for LLM tools risk multi-megabyte single-tool-call reads]]
- [[Non-blocking usage capture fire-and-forget async writes + a serialized promise queue for race-safe RMW]]

**Relations:**
- Vinnstack — *HAS_COMPONENT* — skill-usage counter
- skill-usage counter — *DISPLAYED_IN* — Skills view
- skill-usage counter — *TRACKS* — SKILL.md
- skill-usage counter — *USED_METHOD* — detection method
- detection method — *ACCUMULATED* — raw stdout
- raw stdout — *CAPPED_AT* — 4MB stdout cap
- raw stdout — *STORED_IN* — in-memory string
- detection method — *PERFORMED* — substring search
- substring search — *FOR* — absolute file path
- absolute file path — *IDENTIFIES* — SKILL.md
- Vinnstack — *HAS_MODE* — ultracode mode
- ultracode mode — *RUNS* — Claude Code
- Claude Code — *USES_OPTION* — `--effort xhigh`
- Claude Code — *USES_OPTION* — `--include-partial-messages`
- ultracode mode — *GENERATES* — stream-json
- 4MB stdout cap — *CAUSED* — missed reads
- detection method — *INVOLVED* — heuristic raw-text substring match
- detection method — *USED* — try/catch
- try/catch — *SWALLOWED* — errors
- skill-usage counter — *FIXED_BY* — new detection method
- new detection method — *INVOLVES* — JSON parsing
- new detection method — *PARSES* — stream-json
- JSON parsing — *EXTRACTS* — tool_use blocks
- tool_use blocks — *IDENTIFY* — Read tool
- Read tool — *HAS_PROPERTY* — `input.file_path`
- new detection method — *AVOIDS* — in-memory string
- new detection method — *AVOIDS* — 4MB stdout cap
- new detection method — *AVOIDS* — substring search
- new detection method — *IS_AN_EXAMPLE_OF* — Parse structured tool_use blocks in a stream-json transcript, never substring-scan raw text
- Parse structured tool_use blocks in a stream-json transcript, never substring-scan raw text — *IS_RELATED_TO* — Dead bundling config outlives the runtime code that read it

%% ai-graph-end %%