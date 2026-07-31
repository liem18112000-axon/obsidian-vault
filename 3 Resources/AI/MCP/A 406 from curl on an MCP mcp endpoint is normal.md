---
ai_hash: 7b61e8ec8859615b
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-16
entities: []
source: session 2026-06-16
status: seedling
tags:
- mcp
- gotcha
- http
- debugging
title: A 406 from curl on an MCP /mcp endpoint is normal
type: lesson
---

# A 406 from curl on an MCP /mcp endpoint is normal

Hitting a streamable-HTTP MCP endpoint (e.g. `http://localhost:3001/mcp`) with a plain `curl` returns **HTTP 406 Not Acceptable** — this is expected, not an error. MCP streamable HTTP requires the client to send specific `Accept` headers (it negotiates JSON / SSE), which a bare curl does not. A 406 therefore confirms the server is up and speaking MCP; it is not a sign the endpoint is broken. To truly health-check, use `claude mcp get <name>` (which performs the proper handshake) rather than reading the curl status code.

## Related

- [[3 Resources/Visual/Excalimate/Running Excalimate locally skills in ~.claudeskills plus MCP server on port 3001]]

%% ai-graph-start %%

**Related notes:**
- [[Running Excalimate locally skills in ~.claudeskills plus MCP server on port 3001]]
- [[claude mcp list health status can be stale; verify MCP reachability with curl]]
- [[Excalimate is AI skills plus an optional MCP server, not one app]]
- [[MCP servers load only at Claude Code startup; skills hot-reload]]
- [[A globally-bootstrapped MCP server loads into every headless claude spawn]]

%% ai-graph-end %%