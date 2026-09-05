---
title: "A2A AgentSkill is advertisement metadata, not executor routing"
created: 2026-08-31
type: concept
status: seedling
source: "session 2026-08-31 test-agent"
tags: [a2a, agent-card, executor, mcp, gotcha, architecture]
---

# A2A AgentSkill is advertisement metadata, not executor routing

In the a2a-sdk, an `AgentSkill` (id · name · description · tags · examples) is **advertisement-only metadata**. It goes into the Agent Card (served at `/.well-known/agent-card.json`) so clients can *discover* capabilities. It does NOT connect to the AgentExecutor — the SDK does not route a skill id to a handler.

Dispatch is entirely up to the executor's single `execute(context, event_queue)`, which inspects the incoming **message** (typically its text) and decides what to do. Card and executor are two separate inputs to the A2A app (`create_agent_card_routes(card)` vs `create_jsonrpc_routes(DefaultRequestHandler(agent_executor=…))`); passing the card to DefaultRequestHandler is reference metadata, not a routing table.

**Consequence / gotcha:** the card is a *promise*, the executor is the *implementation*, and nothing keeps them in sync. They can drift — an agent can advertise a skill it never handles (seen in the test-agent repo: `search-memory` and `get-note` are advertised but have no executor branch). Keeping the card honest is developer discipline.

Contrast: **MCP tools** DO map tool-name → handler function directly; that name→function surface in the test-agent lives on the bridge/MCP side, not on the A2A skill card. Mental model: Agent Card = menu, Executor = kitchen. Related: [[test-agent two A2A agents share a skeleton but diverge in domain engines]].

## Related

- [[test-agent two A2A agents share a skeleton but diverge in domain engines]]
