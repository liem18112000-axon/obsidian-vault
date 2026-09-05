---
title: "mcp Python SDK 2.x renamed FastMCP to MCPServer"
created: 2026-08-27
type: lesson
status: seedling
source: "session 2026-08-27"
tags: [mcp, python, gotcha, sdk]
---

# mcp Python SDK 2.x renamed FastMCP to MCPServer

In the `mcp` Python SDK **2.x**, the ergonomic server class `FastMCP` was **renamed to `MCPServer`**. Import it as `from mcp.server.mcpserver import MCPServer`. The old path `from mcp.server.fastmcp import FastMCP` now raises `ModuleNotFoundError` with a message pointing at the migration guide (or pin `mcp<2` to keep v1 code running).

The decorator API is otherwise familiar: `mcp = MCPServer("name", version=..., instructions=...)`, `@mcp.tool()` on an async function (registers it; the function stays directly callable, which is handy in tests), and `mcp.run(transport="stdio")` to serve.

Gotcha on the `Tool` objects returned by `await mcp.list_tools()`: the JSON Schema attribute is **`tool.input_schema`** (snake_case) in 2.x, not `tool.inputSchema`. The old camelCase name raises `AttributeError`.

See [[A2A-to-MCP bridge is an MCP stdio server that is also an A2A client]].

## Related

- [[A2A-to-MCP bridge is an MCP stdio server that is also an A2A client]]
