---
title: "Server-driven Yes/No in MCP: ctx.elicit + an injected Context param"
created: 2026-08-29
type: howto
status: seedling
source: "session 2026-08-28, test-agent elicitation gates"
tags: [mcp, elicitation, claude-code, agents, python]
---

# Server-driven Yes/No in MCP: ctx.elicit + an injected Context param

To make an MCP SERVER drive a Yes/No dialog (human-in-the-loop confirm gate) instead of the client deciding, use **elicitation**: during a tool call the server sends `elicitation/create` to the client and awaits the answer.

Python SDK (`mcp.server.mcpserver`):
- A tool receives the request context by adding a param annotated `Context`; the framework injects it and EXCLUDES it from the tool's public input schema. Detection is `issubclass(candidate, Context)` over the annotation's union args, so `ctx: Context | None = None` works AND stays optional — inject in production, `None` for direct unit-test calls (existing tests unchanged).
- `res = await ctx.elicit(message, SchemaModel)` where SchemaModel is a Pydantic model with PRIMITIVE fields only (e.g. `class Confirm(BaseModel): proceed: bool`). Result: `res.action` in {accept, decline, cancel}; `res.data` (the validated model) only when accept.
- Wrap the confirm helper to degrade gracefully: `try: res = await ctx.elicit(...) except Exception: return None` — elicitation only works if the CLIENT advertises the capability; None => caller proceeds. Decline/cancel => treat as No; a declining gate should short-circuit BEFORE any side-effecting call.

Verify the deployed server over Streamable HTTP: tools/list should show the tool WITHOUT the ctx arg (`define_plan args = [answer, context_id]`), proving injection.

Note the split: server elicits the confirm GATES (start? approve? implement?); the client still presents multi-round interrogation questions and collects free-form answers via subsequent tool calls. See also [[Ship a workflow trigger from an MCP server (no client setup) via server instructions + prompts]].

## Related

- [[Ship a workflow trigger from an MCP server (no client setup) via server instructions + prompts]]
