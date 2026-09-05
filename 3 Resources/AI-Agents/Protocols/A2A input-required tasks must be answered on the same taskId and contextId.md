---
title: "A2A input-required tasks must be answered on the same taskId and contextId"
created: 2026-08-27
type: lesson
status: seedling
source: "session 2026-08-27"
tags: [a2a, agents, state, gotcha]
---

# A2A input-required tasks must be answered on the same taskId and contextId

A2A models a multi-turn (human-in-the-loop) exchange as a **paused Task**: the agent replies with `status.state == "input-required"` and waits. To resume it, the client must send its next message on the **same `taskId` AND the same `contextId`** — a new task or a fresh context starts a different conversation instead of answering the pending one.

Implication for a bridge/adapter whose own calls are stateless (e.g. an MCP tool invoked independently each time): it must **persist the mapping `context_id -> task_id`** across calls, reuse the stored `task_id` when forwarding an answer, and clear it once `status.state == "completed"`. Without that, every answer starts a new interrogation and the task never advances.

See [[A2A message/send returns either a Message or a Task envelope]], [[A2A-to-MCP bridge is an MCP stdio server that is also an A2A client]].

## Related

- [[A2A message/send returns either a Message or a Task envelope]]
- [[A2A-to-MCP bridge is an MCP stdio server that is also an A2A client]]
