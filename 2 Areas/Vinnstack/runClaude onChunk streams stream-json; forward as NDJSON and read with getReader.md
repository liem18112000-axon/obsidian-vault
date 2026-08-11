---
ai_hash: 77512775ac8a1005
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-18
entities: []
source: Vinnstack session 2026-07-18
status: seedling
tags:
- vinnstack
- streaming
- claude-cli
- nextjs
- ndjson
- interrogation
title: runClaude onChunk streams stream-json; forward as NDJSON and read with getReader
type: howto
---

# runClaude onChunk streams stream-json; forward as NDJSON and read with getReader

How the Vinnstack Interrogation Room streams AI generations live end-to-end (2026-07):

1. **Runner primitive already existed**: `lib/ai/claudeRun.ts` `runClaude(...)` takes an optional `overrides.onChunk`. When present it switches the spawned `claude -p` to `--output-format stream-json --include-partial-messages --verbose` and calls `onChunk(line)` with human-readable lines via `describeStreamEvent` (assistant text/thinking deltas pass through as text; a completed tool_use becomes `→ Tool(summary)`; tool_result becomes `↳ …`). The single `result`-type line still carries the same envelope `--output-format json` would, so the returned text and all downstream parsing are unchanged. Pass NO onChunk → old buffered behaviour.

2. **Thread onChunk** through each generation function (generatePrdLive, generateInterrogation, generateProcessFlow, splitStory, approvePrdToJira) into `interrogationOverrides(onChunk)`. The short JSON-parsed critic pass deliberately does NOT get onChunk (must stay --output-format json).

3. **Route streams NDJSON**: the generate route returns `new Response(new ReadableStream(...))` emitting one JSON object per line: `{t:"chunk",s}` for progress, then exactly one terminal `{t:"done",interrogation}` or `{t:"error",error,status}`. HTTP status is 200 once the stream opens; errors carry their status in-band. Pre-stream validation (missing epic) still returns a plain 4xx JSON body.

4. **Client reads with getReader()**: a shared `readGenerateStream(res, onChunk)` line-buffers `res.body.getReader()` + TextDecoder, calls onChunk per chunk line, resolves the terminal message to `{ok, interrogation, error, status}`. (Same transport as `/api/claude/chat`.)

5. **UI**: `GenerationProgress` gained a `live?: string` prop → auto-scrolling mono console; InterrogationView accumulates the running tasks stream into `liveLog` state keyed by task id (cleared in finally so one jobs log never bleeds into the next).

Gotcha: converting a buffered JSON route to a stream breaks every test/caller doing `r.json()` / checking `r.status` — update them to parse the NDJSON terminal line, and `expect.any(Function)` for the new onChunk arg in `toHaveBeenCalledWith`.

## Related

- [[Integrate a third-party skill's instruction, do not run its curl-bash installer]]
- [[Headless claude -p loads the user global CLAUDE.md; isolate per-selection AI transforms with delimiters + data-guard]]

%% ai-graph-start %%

**Related notes:**
- [[Vinnstack AI calls are stateless headless claude CLI runs, not an agent runtime]]
- [[Headless claude -p loads the user global CLAUDE.md; isolate per-selection AI transforms with delimiters + data-guard]]
- [[vinnstack spawns the local claude CLI for subscription-authenticated automation]]
- [[Vinnstack per-request claude CLI spawn has a ~12s cold-start floor, model-independent]]
- [[Parse structured tool_use blocks in a stream-json transcript, never substring-scan raw text]]

%% ai-graph-end %%