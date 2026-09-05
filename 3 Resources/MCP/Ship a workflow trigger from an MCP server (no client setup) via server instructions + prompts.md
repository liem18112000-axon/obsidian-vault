---
title: "Ship a workflow trigger from an MCP server (no client setup) via server instructions + prompts"
created: 2026-08-28
type: lesson
status: seedling
source: "session 2026-08-28, test-agent MCP-only trigger"
tags: [mcp, claude-code, agents, design-decision]
---

# Ship a workflow trigger from an MCP server (no client setup) via server instructions + prompts

Requirement: a FRESH client that has only connected the MCP server(s) — no CLAUDE.md, no slash-command files — should run a whole workflow by saying "test <JIRA>". CLAUDE.md rules and .claude/commands/*.md are CLIENT-side setup and don't meet this. The trigger must be delivered BY THE SERVER. Two levers do it, both zero client-setup:

1. **MCPServer `instructions`** — the initialize result's instructions string is surfaced by the client to the model (in Claude Code it appears as an "MCP Server Instructions" block in the system prompt). Put the trigger + workflow there → free-text "test LUZ-158390" is recognized. Append it to each server's instructions.

2. **MCP `prompts`** — a server-registered prompt (`@mcp.prompt()` in the python SDK's `mcpserver.MCPServer`; returns a string / messages) surfaces in Claude Code as a slash command `/mcp__<server>__<prompt>`. This is the discoverable, reliable "server-shipped command."

Gotchas / how-to:
- The python SDK decorator is `@mcp.prompt()` (must be CALLED — `@mcp.prompt` bare raises). Prompt args are strings.
- If the workflow spans MULTIPLE servers/agents (e.g. gather on one bridge, implement on another), put the same trigger instructions + `test` prompt on BOTH bridges and state that both must be connected — the model has all connected tools available regardless of which prompt/instructions it read.
- Keep one source of truth for the wording in a shared module both bridges import.
- Verify the DEPLOYED server actually serves it with a live MCP handshake over Streamable HTTP: POST initialize (Accept: application/json, text/event-stream; Bearer), capture `mcp-session-id`, POST notifications/initialized, POST prompts/list — response is SSE `data: {json}`. Confirmed capabilities include `prompts` and prompts/list returns the name.

See also [[Natural-language triggers in Claude Code are CLAUDE.md rules, not hooks]] (that's the client-side alternative this deliberately replaced).

## Related

- [[Natural-language triggers in Claude Code are CLAUDE.md rules]]
- [[not hooks]]
