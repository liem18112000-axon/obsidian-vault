---
title: "MCP stdio transport cannot be hosted remotely; use Streamable HTTP"
created: 2026-08-27
type: lesson
status: seedling
source: "session 2026-08-27"
tags: [mcp, transport, cloud-run, remote, gotcha]
---

# MCP stdio transport cannot be hosted remotely; use Streamable HTTP

MCP has two client-facing transports and only one is remotely hostable. **stdio** — the client (Claude Desktop / Claude Code) spawns the server as a local subprocess and talks over its stdin/stdout — cannot run on a remote host, because a remote container has no stdin/stdout pipe back to the client. To host an MCP server remotely (Cloud Run, a VM, etc.) it must serve over a **network transport: Streamable HTTP** (endpoint like `/mcp`), and the client connects to it as a *remote connector* by URL (`claude mcp add --transport http <name> <url>`, or a custom connector on claude.ai) rather than a local command.

In the mcp Python SDK 2.x this is `mcp.run(transport="streamable-http", host="0.0.0.0", port=..., stateless_http=...)` or mounting `mcp.streamable_http_app()` (a Starlette app) under uvicorn. Same server object, just a different transport at startup.

See [[A2A-to-MCP bridge is an MCP stdio server that is also an A2A client]], [[mcp Python SDK 2.x renamed FastMCP to MCPServer]].

## Related

- [[A2A-to-MCP bridge is an MCP stdio server that is also an A2A client]]
- [[mcp Python SDK 2.x renamed FastMCP to MCPServer]]
