---
ai_hash: 3927580b15dafe2d
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-02
entities: []
source: session 2026-07-02 — Polaris in Vinnstack
status: seedling
tags:
- vinnstack
- polaris
- mcp
- claude-code
- permissions
title: Making Polaris MCP tools reachable by Vinnstack's spawned agent (discovery
  + allowlist)
type: howto
---

# Making Polaris MCP tools reachable by Vinnstack's spawned agent (discovery + allowlist)

To make Polaris (ePost) MCP agents/skills usable by the agent Vinnstack SPAWNS (its chat "AI Agent mode"), two independent things must line up:

1. **Discovery** — Vinnstack spawns `claude -p` WITHOUT `--strict-mcp-config`, so the spawned agent auto-loads MCP servers from the user config `~/.claude.json` (cwd-independent). A global `polaris bootstrap` writes the `polaris` server there, so discovery is automatic — provided the tunnel is up (`polaris up`; server at http://localhost:3003/mcp).

2. **Permission** — the spawn runs `--permission-mode dontAsk` with an explicit `--allowedTools` allowlist (CHAT_ALLOWED_TOOLS in lib/ultracodeRunner.ts, applied via permissionArgs() in app/api/claude/chat/route.ts). Under dontAsk, anything not allowlisted is auto-DENIED. So an MCP server being discovered is NOT enough — its tools must be allowlisted or the agent silently can't call them.

The fix: add the server-level entry `"mcp__polaris"` to CHAT_ALLOWED_TOOLS. Claude Code names MCP tools `mcp__<serverKey>__<tool>`; listing just `mcp__<serverKey>` allows the whole server (same pattern as the existing `mcp__claude_ai_Atlassian`). The serverKey is the mcpServers JSON key — here `polaris` (from ~/.claude.json / ~/.mcp.json), NOT the display name "polaris-mcp".

Not touched: the Interrogation Room (lib/interrogationRunner.ts) builds its own narrow per-call tool lists (Read/Grep/Glob + specific Jira tools) — Polaris deliberately stays out of those focused passes.

Runtime checklist to use it: `polaris up` (tunnel on) → global bootstrap done (server in ~/.claude.json) → restart the Vinnstack server so the new allowlist constant is loaded → the spawned chat agent can now call Polaris tools.

## Related

- [[polaris-cli 0.2.0 operational gotchas (bootstrap scope]]
- [[status]]
- [[up]]
- [[agents)]]

%% ai-graph-start %%

**Related notes:**
- [[Vinnstack Polaris integration is three passive touchpoints]]
- [[polaris-cli 0.2.0 operational gotchas (bootstrap scope, status, up, agents)]]
- [[Polaris 0.2.0 serves agentsskillsrules over an MCP tunnel]]
- [[Polaris MCP is search-only 5 tools, no list-all, driven over HTTP JSON-RPC not the CLI]]
- [[Polaris MCP moved to hosted endpoint polaris-mcp.epost.ch (requires auth)]]

%% ai-graph-end %%