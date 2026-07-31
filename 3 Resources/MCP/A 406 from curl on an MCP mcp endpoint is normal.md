---
title: "A 406 from curl on an MCP /mcp endpoint is normal"
created: 2026-06-16
type: lesson
status: seedling
source: "session 2026-06-16"
tags: [mcp, gotcha, http, debugging]
---

# A 406 from curl on an MCP /mcp endpoint is normal

Hitting a streamable-HTTP MCP endpoint (e.g. `http://localhost:3001/mcp`) with a plain `curl` returns **HTTP 406 Not Acceptable** — this is expected, not an error. MCP streamable HTTP requires the client to send specific `Accept` headers (it negotiates JSON / SSE), which a bare curl does not. A 406 therefore confirms the server is up and speaking MCP; it is not a sign the endpoint is broken. To truly health-check, use `claude mcp get <name>` (which performs the proper handshake) rather than reading the curl status code.

## Related

- [[Running Excalimate locally: skills in ~/.claude/skills plus MCP server on port 3001]]
