---
ai_hash: 73f505d636ed71ca
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-16
entities: []
source: session 2026-06-16
status: seedling
tags:
- claude-code
- mcp
- skills
- gotcha
title: MCP servers load only at Claude Code startup; skills hot-reload
type: lesson
---

# MCP servers load only at Claude Code startup; skills hot-reload

Claude Code discovers **skills** by scanning the skills folders continuously, so a newly added or edited `SKILL.md` becomes available within the same running session (hot-reload). **MCP servers** are different: they are loaded once at startup, so adding one with `claude mcp add` does NOT expose its tools to the session you ran the command in — you must restart Claude Code to pick up the new MCP tools.

So after wiring a new MCP server, expect: skills usable immediately, MCP tools only after restart. `claude mcp list` / `claude mcp get <name>` reporting "Connected" only confirms the server is reachable, not that the current session has loaded its tools.

Related: [[3 Resources/AI/Claude-Code/Claude Code holds an open handle on every skills folder under ~.claude]].

## Related

- [[3 Resources/AI/Claude-Code/Claude Code holds an open handle on every skills folder under ~.claude]]

%% ai-graph-start %%

**Related notes:**
- [[Claude Code holds an open handle on every skills folder under ~.claude]]
- [[Running Excalimate locally skills in ~.claudeskills plus MCP server on port 3001]]
- [[A globally-bootstrapped MCP server loads into every headless claude spawn]]
- [[Polaris MCP is search-only 5 tools, no list-all, driven over HTTP JSON-RPC not the CLI]]
- [[Making Polaris MCP tools reachable by Vinnstack's spawned agent (discovery + allowlist)]]

%% ai-graph-end %%