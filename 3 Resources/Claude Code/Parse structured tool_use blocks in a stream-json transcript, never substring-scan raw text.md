---
title: "Parse structured tool_use blocks in a stream-json transcript, never substring-scan raw text"
created: 2026-07-08
type: lesson
status: seedling
source: "vinnstack session, 2026-07-08"
tags: [claude-code, stream-json, agent-sdk, parsing]
---

# Parse structured tool_use blocks in a stream-json transcript, never substring-scan raw text

When you need to detect "did the agent do X" (e.g. "did it Read this specific file", "did it call this tool") from a Claude Code / Agent SDK `stream-json` transcript, parse the structured `assistant` message events and inspect their `content` array for `tool_use` blocks matching the `name`/`input` you care about — never regex or substring-scan the raw text output.

Why: with `--include-partial-messages`, tool inputs also stream as fragmented `input_json_delta` pieces interleaved with other JSON syntax, so a literal value (like a file path) may never appear as one clean contiguous substring in the raw stream. The complete, authoritative version of that value always arrives intact inside one `{"type":"assistant",...}` event'\''s `message.content[].input`, emitted once per full assistant turn regardless of whether partial streaming is also on. Parsing that one JSON object per line is both simpler and correct at any output size — no accumulation buffer, no size cap needed, unlike scanning raw concatenated text.

## Related
[[Vinnstack skill-usage counter missed reads past a 4MB stdout cap]]
