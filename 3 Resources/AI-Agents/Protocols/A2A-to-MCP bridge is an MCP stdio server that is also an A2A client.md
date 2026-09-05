---
title: "A2A-to-MCP bridge is an MCP stdio server that is also an A2A client"
created: 2026-08-27
type: concept
status: seedling
source: "session 2026-08-27"
tags: [a2a, mcp, bridge, agents, claude]
---

# A2A-to-MCP bridge is an MCP stdio server that is also an A2A client

Claude (and Claude Code / Desktop) is an **MCP** client; many autonomous agents expose themselves over **A2A** (Agent2Agent, JSON-RPC + SSE, an Agent Card at `/.well-known/agent-card.json`). The two protocols do not interoperate directly. The bridge that lets Claude drive an A2A agent is a single process that is **both**: an MCP server on stdio (the face Claude launches and calls tools on) and an A2A client (which forwards each MCP tool call to the agent's `message/send` over HTTP).

Key consequences:
- The bridge runs **client-side**, next to Claude — the agent server is unchanged and needs no MCP.
- Each MCP tool maps to one A2A operation; the bridge translates arguments in and the A2A reply out.
- Auth passes through: the bridge attaches the agent's bearer token on each HTTP call.

Implemented in the `knowledge_gathering` test-agent as `knowledge_gathering/bridge/` (a2a_client.py = A2A half, no mcp import; mcp_server.py = MCPServer tools). See [[mcp Python SDK 2.x renamed FastMCP to MCPServer]], [[A2A message/send returns either a Message or a Task envelope]], [[A2A input-required tasks must be answered on the same taskId and contextId]].

## Related

- [[mcp Python SDK 2.x renamed FastMCP to MCPServer]]
- [[A2A message/send returns either a Message or a Task envelope]]
- [[A2A input-required tasks must be answered on the same taskId and contextId]]
