---
title: "A2A message/send returns either a Message or a Task envelope"
created: 2026-08-27
type: observation
status: seedling
source: "session 2026-08-27"
tags: [a2a, json-rpc, agents]
---

# A2A message/send returns either a Message or a Task envelope

A JSON-RPC `message/send` call to an A2A agent (a2a-sdk 1.x, proto v0.3 JSON) comes back in **one of two shapes**, distinguished by `result.kind`:

- **Message** (`result.kind == "message"`) — a one-shot reply. Text lives in `result.parts[].text`. `result.taskId` and `result.contextId` are present. There is no status/state.
- **Task** (`result.kind == "task"`) — a longer-lived unit of work. The task id is `result.id` (not `taskId`), lifecycle is `result.status.state` (e.g. `input-required`, `completed`), and the agent's text is in `result.status.message.parts[].text`. There may also be `result.artifacts[].parts[]` and a `result.history[]`.

A robust client extracts text from **both** shapes: scan `parts`, then `status.message.parts`, then `artifacts[].parts`, then agent turns in `history`. Parts use `{"kind":"text","text":...}`.

See [[A2A input-required tasks must be answered on the same taskId and contextId]], [[A2A-to-MCP bridge is an MCP stdio server that is also an A2A client]].

## Related

- [[A2A input-required tasks must be answered on the same taskId and contextId]]
- [[A2A-to-MCP bridge is an MCP stdio server that is also an A2A client]]
