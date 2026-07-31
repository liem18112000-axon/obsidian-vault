---
title: Skills vs Hooks vs MCP vs subagents
created: 2026-06-11
type: concept
status: seedling
source: research session 2026-06-11
tags:
  - claude-code
  - skills
  - hooks
  - mcp
  - decision
aliases:
  - When to use a skill vs hook
  - Claude primitive decision
---

# Skills vs Hooks vs MCP vs subagents

**Pick the primitive by *who triggers it* and *whether it must always run*: skills are model-invoked knowledge/recipes, hooks are deterministic event-fired guardrails, MCP servers are live tool connections, and subagents are isolated workers.** Most affiliate setups use a *combination*, not one.

| Primitive | Triggered by | Guarantee | Best affiliate fit |
|---|---|---|---|
| **Skill** | model or `/name` | advisory (may run) | the Accesstrade "playbook" + script |
| **Hook** | lifecycle event | deterministic (always) | guard link-mints, log ledger, postback sink |
| **MCP server** | model calls its tools | live connection, schema'd tools | a hosted Accesstrade tool server / Slack |
| **Subagent** | delegated by main loop | isolated context | heavy report crawls, parallel content drafts |

## Decision flow

```mermaid
flowchart TD
    Q[What do I need?] --> A{Must it ALWAYS<br/>happen on an event?}
    A -- yes --> H[Hook]
    A -- no --> B{Reusable recipe /<br/>knowledge?}
    B -- yes --> S[Skill]
    B -- no --> C{Live external tool<br/>with its own schema?}
    C -- yes --> M[MCP server]
    C -- no --> D{Big/parallel job<br/>needing own context?}
    D -- yes --> SA[Subagent]
```

## How they compose for Accesstrade

- A **skill** (`accesstrade`) holds the recipes and the script that talks to the API.
- **Hooks** enforce the rails around it (no minting on paused campaigns; log every link; forward conversions).
- An **MCP server** is worth it only if you want the API available as typed tools across many sessions/clients, or to bridge Slack/notifications.
- **Subagents** (`context: fork`) absorb the long, windowed report pulls so the main chat stays lean.

> [!tip]
> Rule of thumb: **knowledge → skill, enforcement → hook, integration → MCP, scale/isolation → subagent.**

## Related

- [[Claude Code Skill anatomy]]
- [[Claude Code hooks event model]]
- [[Designing an Accesstrade skill for Claude Code]]
- [[Accesstrade API Integration - MOC]]
