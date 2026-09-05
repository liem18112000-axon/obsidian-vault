---
title: "a2a-sdk serves the agent card at agent-card.json (new) or agent.json (old)"
created: 2026-08-27
type: lesson
status: seedling
source: "test-agent server.py — session 2026-08-27"
tags: [a2a, a2a-sdk, agent-card, gotcha, python]
---

# a2a-sdk serves the agent card at agent-card.json (new) or agent.json (old)

The **a2a-sdk** (a2a-python) server serves the Agent Card at a **version-dependent** well-known path: recent SDKs use `/.well-known/agent-card.json` (matching the updated A2A spec), older ones use `/.well-known/agent.json`. A client that fetches the wrong path gets a 404 and "cannot discover the agent".

**Fix:** pin an exact a2a-sdk version, then curl both paths after boot and record which one your version serves before wiring any client (e.g. local Claude as the A2A client).

## Verified on a2a-sdk 1.1.2 (2026-08)

The 1.x API is very different from 0.2.x quickstarts — the old `A2AStarletteApplication`, `new_agent_text_message`, and pydantic camelCase card **do not exist**. Confirmed facts:

- **`a2a.types.AgentCard` IS `a2a_pb2.AgentCard`** — a **protobuf** message. Fields are **snake_case** (`default_input_modes`, `default_output_modes`), NOT camelCase. There is **no `url` field**; the endpoint lives in `supported_interfaces=[AgentInterface(url=..., protocol_binding=TransportProtocol.JSONRPC)]`.
- **Card path is `/.well-known/agent-card.json`** (constant `a2a.utils.AGENT_CARD_WELL_KNOWN_PATH`). JSON-RPC default url is `/` (`a2a.utils.DEFAULT_RPC_URL`).
- **HTTP server needs the extra**: `a2a-sdk[http-server]` (pulls `starlette` + `sse-starlette`). Base install has no ASGI app.
- **No convenience app class.** Build routes and mount on Starlette yourself: `create_agent_card_routes(card) + create_jsonrpc_routes(handler, "/", enable_v0_3_compat=True)` → `Starlette(routes=...)`. `DefaultRequestHandler(agent_executor, task_store, agent_card)` (card is a required 3rd arg).
- **Method names:** native JSON-RPC method is **`SendMessage`**, not `message/send`. The classic `message/send` / `message/stream` only exist when you pass **`enable_v0_3_compat=True`** — do this for compatibility with existing A2A clients (incl. Claude's).
- **Executor reply:** `new_agent_text_message` is gone. Use `TaskUpdater(event_queue, task_id, context_id).new_agent_message([a2a_pb2.Part(text=...)])`. For a simple immediate reply, `enqueue_event(that_message)`; do NOT call `submit()/start_work()` without first enqueuing a Task or you get `InvalidAgentResponseError: Agent should enqueue Task before TaskStatusUpdateEvent`.

## Related

- [[A remote A2A agent needs its own connectors because MCP is client-side]]
