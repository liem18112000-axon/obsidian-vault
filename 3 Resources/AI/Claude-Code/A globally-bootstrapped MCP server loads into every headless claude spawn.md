---
ai_hash: a30130ed06238423
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-14
entities: []
source: session 2026-07-14
status: seedling
tags:
- mcp
- claude-code
- gotcha
- polaris
- strict-mcp-config
title: A globally-bootstrapped MCP server loads into every headless claude spawn
type: lesson
---

# A globally-bootstrapped MCP server loads into every headless claude spawn

Gotcha (Vinnstack, 2026-07-14): after `polaris bootstrap` wired the Polaris MCP server into ~/.claude.json (user scope), EVERY headless `claude -p` the app spawns started loading that MCP server — including the tool-less background transforms (chat summarize, extract-skill, follow-ups, journal rewrite). Symptom: /api/chat/summarize returned 502 ("no summary produced") even though claude exited 0, and runs were slow (~32s+). Loading MCP adds startup latency AND exposes tools the model can wander into instead of returning the JSON-only payload the transform asked for.

Fix: pass `--strict-mcp-config` (with NO `--mcp-config`) on those spawns — "only use MCP servers from --mcp-config, ignoring all other MCP configurations" → loads ZERO MCP servers. Apply to background/transform spawns; keep MCP on the real interactive agent spawn only.

Lesson: bootstrapping an MCP server GLOBALLY (user scope) silently changes the behavior of every process that reads that config, not just the intended editor session. Prefer project-scope wiring, or explicitly opt tool-less spawns out with --strict-mcp-config.

Verification note: route health confirmed (short-transcript fast path → 422 in 0.1s; tsc clean), but a full end-to-end summarize success wasn't captured in-session due to dev model latency + leftover claude processes from repeated test hits. Related: [[Verify-after-mutate re-probe external state before reporting a CLI toggle succeeded]].

%% ai-graph-start %%

**Related notes:**
- [[polaris-cli 0.2.0 operational gotchas (bootstrap scope, status, up, agents)]]
- [[Making Polaris MCP tools reachable by Vinnstack's spawned agent (discovery + allowlist)]]
- [[Headless claude -p loads the user global CLAUDE.md; isolate per-selection AI transforms with delimiters + data-guard]]
- [[Polaris MCP is search-only 5 tools, no list-all, driven over HTTP JSON-RPC not the CLI]]
- [[Polaris MCP moved to hosted endpoint polaris-mcp.epost.ch (requires auth)]]

%% ai-graph-end %%