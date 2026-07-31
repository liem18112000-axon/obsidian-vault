---
title: "MCP servers load only at Claude Code startup; skills hot-reload"
created: 2026-06-16
type: lesson
status: seedling
source: "session 2026-06-16"
tags: [claude-code, mcp, skills, gotcha]
---

# MCP servers load only at Claude Code startup; skills hot-reload

Claude Code discovers **skills** by scanning the skills folders continuously, so a newly added or edited `SKILL.md` becomes available within the same running session (hot-reload). **MCP servers** are different: they are loaded once at startup, so adding one with `claude mcp add` does NOT expose its tools to the session you ran the command in — you must restart Claude Code to pick up the new MCP tools.

So after wiring a new MCP server, expect: skills usable immediately, MCP tools only after restart. `claude mcp list` / `claude mcp get <name>` reporting "Connected" only confirms the server is reachable, not that the current session has loaded its tools.

Related: [[3 Resources/AI/Claude-Code/Claude Code holds an open handle on every skills folder under ~.claude]].

## Related

- [[3 Resources/AI/Claude-Code/Claude Code holds an open handle on every skills folder under ~.claude]]
