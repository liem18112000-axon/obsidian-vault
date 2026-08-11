---
ai_hash: 9008f4956c42c4e5
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-12
entities: []
source: session 2026-06-12, accesstrade_integration mcp_server
status: seedling
tags:
- mcp
- llm
- architecture
- fastmcp
- python
- adapter
title: Expose an app as an MCP server by wrapping the same services container the
  web/CLI use
type: howto
---

# Expose an app as an MCP server by wrapping the same services container the web/CLI use

**To add an MCP server to an existing app, make it a thin ADAPTER over the same application-services container the web app and CLIs already use — don'\''t re-implement the logic in the tools.** Each MCP tool just delegates to a use-case builder or repository and maps errors; the tools share one DB/cache/client, so actions taken via the LLM land in the same store as everything else.

Pattern (used for the Accesstrade console — FastMCP / `mcp` SDK):
- One `Services` container (DB + repositories + API client) built once, lazily, per process; reused by the FastAPI app, the CLIs, AND the MCP server. The container must not import the web framework so the MCP server can reuse it without pulling FastAPI.
- `from mcp.server.fastmcp import FastMCP; mcp = FastMCP(name)`; each function decorated `@mcp.tool()`. The function'\''s type hints become the input schema and its docstring becomes the tool description — so write real docstrings.
- Entry point `python -m mcp_server` calls `mcp.run()` (stdio transport by default). Load `./.env` before `mcp.run()` so the key/DB resolve like the CLIs; the client'\''s `cwd` must be the repo root.
- Translate domain errors (missing key, API failure) into a clean `RuntimeError` inside each tool so the client shows a readable tool error, not a stack trace.
- Split tools into key-free (local store reads) vs live (need the API key) in the docs so users know what works offline.

Gotchas: `mcp` has no `__version__` attribute (use `importlib.metadata.version('mcp')`); FastMCP'\''s `@mcp.tool()` returns the original function, so tools stay directly callable in unit tests; list tools without a client via `asyncio.run(mcp.list_tools())`.

Relates to [[Wrap a two-generation API as one shared transport plus one client per generation]] (same 'shared core, thin adapters' instinct).

## Related

- [[Wrap a two-generation API as one shared transport plus one client per generation]]

%% ai-graph-start %%

**Related notes:**
- [[Skills vs Hooks vs MCP vs subagents]]
- [[MCP servers load only at Claude Code startup; skills hot-reload]]
- [[Wrap a two-generation API as one shared transport plus one client per generation]]
- [[pip install does not bundle templatesstatic referenced relative to a package]]
- [[Wiring an external MCP-serving CLI into a Next.js app status-on-provider, actions-on-dedicated-route]]

%% ai-graph-end %%