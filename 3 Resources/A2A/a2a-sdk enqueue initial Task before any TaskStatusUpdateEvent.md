---
title: "a2a-sdk: enqueue initial Task before any TaskStatusUpdateEvent"
created: 2026-08-27
type: lesson
status: seedling
source: "session 2026-08-27 test-agent"
tags: [a2a, a2a-sdk, gotcha, agents, python]
---

# a2a-sdk: enqueue initial Task before any TaskStatusUpdateEvent

In a2a-sdk 1.x (protobuf/v2 handlers), an `AgentExecutor` that drives the Task lifecycle must **enqueue an initial `Task` object before emitting any `TaskStatusUpdateEvent`** (i.e. before `TaskUpdater.requires_input()` / `complete()` / even `submit()`). `submit()` is itself a status update, so it cannot be the first event — doing so raises `InvalidAgentResponseError: Agent should enqueue Task before TaskStatusUpdateEvent event`.

Fix on the first turn of a task:
```python
from a2a.helpers.proto_helpers import new_task_from_user_message
if context.current_task is None:  # turn 1
    await event_queue.enqueue_event(new_task_from_user_message(context.message))
updater = TaskUpdater(event_queue, context.task_id, context.context_id)
await updater.requires_input(updater.new_agent_message([...]))  # now legal
```
On a continuation turn the Task already exists (`context.current_task` is set), so skip the enqueue.

Contrast: enqueuing a plain **Message** (`updater.new_agent_message(...)` straight onto the queue, as a one-shot reply does) does NOT require a prior Task — only task **status** transitions do. So one-shot "reply and return" executors never hit this; only multi-turn ones using input-required/complete do.

Discovered wiring the Knowledge-Refinement multi-turn dialogue (test-agent, LUZ-159671). See [[A2A multi-turn human-in-the-loop via input-required Task state]].

## Related

- [[A2A multi-turn human-in-the-loop via input-required Task state]]
