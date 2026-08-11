---
ai_hash: a711ef674123804d
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-14
entities:
- Vinnstack
- Polaris
- Vinnstack Polaris integration
- statusOnly provider card
- lib/account/authProviders.ts
- polarisProvider
- CLI
- ~/.polaris/state.json
- Google ADC
- mcp__polaris
- CHAT_ALLOWED_TOOLS
- lib/ultracode/ultracodeRunner.ts
- claude
- Polaris tools
- AGENTS.md pointer block
- Vinnstack's Bitbucket Agent Kernel
- lib/ultracode/agentKernel.ts
- MCP
- company-wide skills system
- doc/polaris-mcp-integration-plan.md
- Polaris orchestration
- Polaris 0.2.0
- agentsskillsrules
- MCP tunnel
- tunnel
- login
- logout
- route runs
- static '@polaris' guidance
source: session 2026-07-14
status: seedling
tags:
- vinnstack
- polaris
- mcp
- integration
title: Vinnstack Polaris integration is three passive touchpoints
type: observation
---

# Vinnstack Polaris integration is three passive touchpoints

As of 2026-07-14, Vinnstack wires Polaris in only **three passive places** — it never surfaces, controls, or routes through Polaris:
1. **statusOnly provider card** (`lib/account/authProviders.ts`, `polarisProvider`) — detects the CLI, reads `~/.polaris/state.json`, TCP-probes the tunnel on :3003, checks Google ADC. `login`/`logout` are no-ops that point back to the CLI.
2. **`mcp__polaris` in `CHAT_ALLOWED_TOOLS`** (`lib/ultracode/ultracodeRunner.ts`) — a spawned `claude` *may* call Polaris tools, but only if the tunnel was brought up separately in a terminal; silent when down.
3. **`AGENTS.md` pointer block** — static '@polaris' guidance the runner doesn't act on.

**Overlap gotcha:** Vinnstack's Bitbucket **Agent Kernel** read-only mirror (`lib/ultracode/agentKernel.ts`) is the conceptual *predecessor* of what Polaris now serves live over MCP. That creates a **replace-vs-coexist decision** — two parallel 'company-wide skills' systems. Recommendation on file: coexist short-term (label clearly), supersede once Polaris coverage is confirmed.

Full phased integration plan: `doc/polaris-mcp-integration-plan.md` (Phases 0-3 = MVP visibility+control; Phase 4 = route runs through Polaris orchestration).

## Related

- [[3 Resources/Work-Side/Polaris/Polaris 0.2.0 serves agentsskillsrules over an MCP tunnel]]

%% ai-graph-start %%

**Related notes:**
- [[Making Polaris MCP tools reachable by Vinnstack's spawned agent (discovery + allowlist)]]
- [[Vinnstack auth providers two patterns and the rule for adding one]]
- [[Wiring an external MCP-serving CLI into a Next.js app status-on-provider, actions-on-dedicated-route]]
- [[Polaris 0.2.0 serves agentsskillsrules over an MCP tunnel]]
- [[Polaris 3003 MCP server is persistent — TCP probe not equal to polaris tunnel state]]

**Relations:**
- Vinnstack Polaris integration — *CONSISTS_OF* — statusOnly provider card
- Vinnstack Polaris integration — *CONSISTS_OF* — mcp__polaris
- Vinnstack Polaris integration — *CONSISTS_OF* — AGENTS.md pointer block
- Vinnstack — *WIRES* — Polaris
- Vinnstack — *DOES_NOT_SURFACE* — Polaris
- Vinnstack — *DOES_NOT_CONTROL* — Polaris
- Vinnstack — *DOES_NOT_ROUTE_THROUGH* — Polaris
- statusOnly provider card — *IS_DEFINED_IN* — lib/account/authProviders.ts
- statusOnly provider card — *IS_IDENTIFIED_AS* — polarisProvider
- statusOnly provider card — *DETECTS* — CLI
- statusOnly provider card — *READS* — ~/.polaris/state.json
- statusOnly provider card — *TCP_PROBES* — tunnel
- statusOnly provider card — *CHECKS* — Google ADC
- login — *IS_NO_OP_FOR* — statusOnly provider card
- logout — *IS_NO_OP_FOR* — statusOnly provider card
- login — *POINTS_TO* — CLI
- logout — *POINTS_TO* — CLI
- mcp__polaris — *IS_PART_OF* — CHAT_ALLOWED_TOOLS
- CHAT_ALLOWED_TOOLS — *IS_DEFINED_IN* — lib/ultracode/ultracodeRunner.ts
- claude — *MAY_CALL* — Polaris tools
- mcp__polaris — *ENABLES* — Polaris tools
- AGENTS.md pointer block — *PROVIDES* — static '@polaris' guidance
- runner — *DOES_NOT_ACT_ON* — AGENTS.md pointer block
- Vinnstack's Bitbucket Agent Kernel — *IS_DEFINED_IN* — lib/ultracode/agentKernel.ts
- Vinnstack's Bitbucket Agent Kernel — *IS_PREDECESSOR_OF* — Polaris
- Polaris — *SERVES* — skills
- Polaris — *SERVES_OVER* — MCP
- Vinnstack's Bitbucket Agent Kernel — *IS_A* — company-wide skills system
- Polaris — *IS_A* — company-wide skills system
- Vinnstack's Bitbucket Agent Kernel — *IS_PARALLEL_TO* — Polaris
- doc/polaris-mcp-integration-plan.md — *IS_A* — full phased integration plan
- doc/polaris-mcp-integration-plan.md — *CONCERNS* — Polaris MCP integration
- Polaris orchestration — *HANDLES* — route runs
- Polaris 0.2.0 — *SERVES* — agentsskillsrules
- Polaris 0.2.0 — *SERVES_OVER* — MCP tunnel
- Polaris 0.2.0 — *IS_A_VERSION_OF* — Polaris

%% ai-graph-end %%