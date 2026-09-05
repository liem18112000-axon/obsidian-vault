---
title: "approve_plan is an agent-side write, unlike knowledge_gathering's read-only approve"
created: 2026-08-28
type: lesson
status: seedling
source: "session 2026-08-28, test_plan_definition M4"
tags: [test-agent, a2a, mcp, bridge, design-decision]
---

# approve_plan is an agent-side write, unlike knowledge_gathering's read-only approve

knowledge_gathering's MCP `approve` tool is READ-ONLY: it just reads get-understanding + get-questions and packages them as the hand-off; the refine loop already persisted everything. So approve lives purely client-side (in Claude) and sends no state-changing A2A message.

test_plan_definition's `approve_plan` is DIFFERENT: it must lock `TestPlan.status = "confirmed"` so the implement stage's gate passes. That is a memory-bank WRITE, and the bridge (client-side) can only reach the bank through the agent over A2A. Therefore approve is an **agent-side operation**: the bridge sends `approve <ctx>`, a new `run_approve` executor route flips the persisted plan to confirmed (+ closes the define session), and the tool returns the confirmed brief.

**Routing gotcha:** the `approve` branch must sit BEFORE the live-define-session / wants_define branch in the executor router, otherwise a still-open define session would swallow "approve <ctx>" as if it were an answer. Explicit gate wins over an in-flight session.

Reuse note: the generic `A2ABridgeClient` and the `_BearerASGIMiddleware` ASGI gate are reused verbatim from knowledge_gathering.bridge across both agents' bridges — only the tool set, instructions, env prefixes (TPD_*), and default port (8081) differ.

## Related

- [[Testing Agent builds each pipeline stage as a package mirroring the knowledge_gathering skeleton]]
