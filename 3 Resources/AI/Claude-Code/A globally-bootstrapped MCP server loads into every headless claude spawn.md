---
title: "A globally-bootstrapped MCP server loads into every headless claude spawn"
created: 2026-07-14
type: lesson
status: seedling
source: "session 2026-07-14"
tags: [mcp, claude-code, gotcha, polaris, strict-mcp-config]
---

# A globally-bootstrapped MCP server loads into every headless claude spawn

Gotcha (Vinnstack, 2026-07-14): after `polaris bootstrap` wired the Polaris MCP server into ~/.claude.json (user scope), EVERY headless `claude -p` the app spawns started loading that MCP server — including the tool-less background transforms (chat summarize, extract-skill, follow-ups, journal rewrite). Symptom: /api/chat/summarize returned 502 ("no summary produced") even though claude exited 0, and runs were slow (~32s+). Loading MCP adds startup latency AND exposes tools the model can wander into instead of returning the JSON-only payload the transform asked for.

Fix: pass `--strict-mcp-config` (with NO `--mcp-config`) on those spawns — "only use MCP servers from --mcp-config, ignoring all other MCP configurations" → loads ZERO MCP servers. Apply to background/transform spawns; keep MCP on the real interactive agent spawn only.

Lesson: bootstrapping an MCP server GLOBALLY (user scope) silently changes the behavior of every process that reads that config, not just the intended editor session. Prefer project-scope wiring, or explicitly opt tool-less spawns out with --strict-mcp-config.

Verification note: route health confirmed (short-transcript fast path → 422 in 0.1s; tsc clean), but a full end-to-end summarize success wasn't captured in-session due to dev model latency + leftover claude processes from repeated test hits. Related: [[Verify-after-mutate re-probe external state before reporting a CLI toggle succeeded]].
