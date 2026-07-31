---
title: "Vinnstack Polaris integration is three passive touchpoints"
created: 2026-07-14
type: observation
status: seedling
source: "session 2026-07-14"
tags: [vinnstack, polaris, mcp, integration]
---

# Vinnstack Polaris integration is three passive touchpoints

As of 2026-07-14, Vinnstack wires Polaris in only **three passive places** — it never surfaces, controls, or routes through Polaris:
1. **statusOnly provider card** (`lib/account/authProviders.ts`, `polarisProvider`) — detects the CLI, reads `~/.polaris/state.json`, TCP-probes the tunnel on :3003, checks Google ADC. `login`/`logout` are no-ops that point back to the CLI.
2. **`mcp__polaris` in `CHAT_ALLOWED_TOOLS`** (`lib/ultracode/ultracodeRunner.ts`) — a spawned `claude` *may* call Polaris tools, but only if the tunnel was brought up separately in a terminal; silent when down.
3. **`AGENTS.md` pointer block** — static '@polaris' guidance the runner doesn't act on.

**Overlap gotcha:** Vinnstack's Bitbucket **Agent Kernel** read-only mirror (`lib/ultracode/agentKernel.ts`) is the conceptual *predecessor* of what Polaris now serves live over MCP. That creates a **replace-vs-coexist decision** — two parallel 'company-wide skills' systems. Recommendation on file: coexist short-term (label clearly), supersede once Polaris coverage is confirmed.

Full phased integration plan: `doc/polaris-mcp-integration-plan.md` (Phases 0-3 = MVP visibility+control; Phase 4 = route runs through Polaris orchestration).

## Related

- [[Polaris 0.2.0 serves agents/skills/rules over an MCP tunnel]]
