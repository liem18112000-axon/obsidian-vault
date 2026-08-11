---
ai_hash: cbd6da6f07692824
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-08
entities: []
source: vinnstack session, 2026-07-08
status: seedling
tags:
- claude-code
- stream-json
- agent-sdk
- parsing
title: Parse structured tool_use blocks in a stream-json transcript, never substring-scan
  raw text
type: lesson
---

# Parse structured tool_use blocks in a stream-json transcript, never substring-scan raw text

When you need to detect "did the agent do X" (e.g. "did it Read this specific file", "did it call this tool") from a Claude Code / Agent SDK `stream-json` transcript, parse the structured `assistant` message events and inspect their `content` array for `tool_use` blocks matching the `name`/`input` you care about — never regex or substring-scan the raw text output.

Why: with `--include-partial-messages`, tool inputs also stream as fragmented `input_json_delta` pieces interleaved with other JSON syntax, so a literal value (like a file path) may never appear as one clean contiguous substring in the raw stream. The complete, authoritative version of that value always arrives intact inside one `{"type":"assistant",...}` event'\''s `message.content[].input`, emitted once per full assistant turn regardless of whether partial streaming is also on. Parsing that one JSON object per line is both simpler and correct at any output size — no accumulation buffer, no size cap needed, unlike scanning raw concatenated text.

## Related
[[Vinnstack skill-usage counter missed reads past a 4MB stdout cap]]

%% ai-graph-start %%

**Related notes:**
- [[Vinnstack skill-usage counter missed reads past a 4MB stdout cap]]
- [[runClaude onChunk streams stream-json; forward as NDJSON and read with getReader]]
- [[Claude Code hooks see no token usage in their payload; read the transcript usage entries instead]]
- [[Headless claude -p loads the user global CLAUDE.md; isolate per-selection AI transforms with delimiters + data-guard]]
- [[Vinnstack AI calls are stateless headless claude CLI runs, not an agent runtime]]

%% ai-graph-end %%