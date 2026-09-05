---
title: "A2A multi-turn human-in-the-loop via input-required Task state"
created: 2026-08-27
type: howto
status: seedling
source: "session 2026-08-27 test-agent"
tags: [a2a, agents, human-in-the-loop, protocol, memory]
---

# A2A multi-turn human-in-the-loop via input-required Task state

An interactive "agent asks clarifying questions → human answers → agent refines" dialogue can be modeled directly on the Agent2Agent (A2A) protocol without inventing new transport: the agent transitions the **Task to the `input-required` state** and emits its questions as the message/artifact; the client (e.g. local Claude relaying to a human) replies with `message/send` carrying the **same `taskId`**, moving the Task back to `working`. Group the whole dialogue under one **`contextId`** so it shares the conversation with whatever produced the input.

Key durability trick: persist the session state (questions/answers/running understanding) to **long-term memory (e.g. a GCS folder keyed by `contextId`)**, not just in-process. Then `tasks/get` / `tasks/resubscribe` resume the dialogue after **a full server restart**, not merely an SSE reconnect. A2A short-term memory (task history + contextId) covers the live turn; the persisted store covers durability.

Contrast with a one-shot A2A skill (single `message/send` → result artifacts): the multi-turn shape is the right fit whenever a human judgement call must be injected mid-task.

Source: designing Step 2 (Knowledge Refinement) of the test-agent Testing Agent (LUZ-159671).

## Related

- [[Ground-then-refine: gathering grounds]]
- [[refinement interprets and confirms]]
