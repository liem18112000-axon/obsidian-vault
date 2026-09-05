---
title: "Agent skeleton = Instruction + Skills-Resources + Tools + Context"
created: 2026-08-17
type: model
status: seedling
source: "session 2026-08-17 agent-framework-skeleton diagram"
tags: [agents, architecture, mcp, skills]
---

# Agent skeleton = Instruction + Skills-Resources + Tools + Context

A minimal mental model for an AI agent: a **stateless** core plus four supplied building blocks. The agent itself holds no state between runs; everything it needs is injected each invocation.

- **Instruction (System prompt)** — WHO the agent is; kept stateless.
- **Skills** (`skills.md`) — HOW it works; playbooks / procedures. Skills reference →
- **Resources** — scripts, data, and assets the skills call on.
- **Tools** — external capabilities, typically via MCP (e.g. Atlassian, Gcloud CLI).
- **Context** — memory / session state passed in for this run.

So: **Agent = Instruction + Skills(→Resources) + Tools + Context.**

The four blocks **converge** into the agent core, which then produces outputs (e.g. a report or a Q/A interrogation). Underlying data/systems (in the Luz case: view-controller → Luz-docs ↔ Luz-vault → JSON Store → MongoDB) are surfaced to the agent through Tools + Context — they 'ground' the answer.

Example downstream use: [[Multi-level altitude reporting from a single-source agent answer]].

## Expanded anatomy (hub-and-spoke)

A fuller whiteboard version puts **Agent/s** at the hub with more spokes than the minimal four:

- **Runtime** — the execution environment the agent runs in.
- **Identity** — who/what the agent authenticates and acts as.
- **Context** decomposes into concrete sources: **CodeBase**, **Memory / History**, **Confluence / JIRA**, **Logs**.
- **Instruction.md** → explicitly **Stateless**.
- **Skills.md** → **Resources (scripts…)**.
- **Tools** → **MCP** → *Polaris / Atlassian*, plus **gcloud CLI**.

**Skill folder layout:** a skill is a folder containing `skill.md` (the HOW) + `script.py`/`.psh` (the runnable resource). This matches how Polaris/Claude-Code skills are packaged.

## Related

- [[Multi-level altitude reporting from a single-source agent answer]]

- [[Multi-level altitude reporting from a single-source agent answer]]
