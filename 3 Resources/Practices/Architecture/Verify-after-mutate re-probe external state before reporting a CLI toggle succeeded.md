---
ai_hash: 325273b01d5daa8b
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-14
entities: []
source: session 2026-07-14
status: seedling
tags:
- cli
- integration
- reliability
- polaris
- pattern
title: 'Verify-after-mutate: re-probe external state before reporting a CLI toggle
  succeeded'
type: lesson
---

# Verify-after-mutate: re-probe external state before reporting a CLI toggle succeeded

When an app wraps a CLI that toggles external state (e.g. `polaris up` starting an MCP tunnel), don't trust the command's exit code alone — **verify the effect before reporting success**. Pattern used in Vinnstack's `polarisCli.tunnelUp()`: run `polaris up`, then re-probe the port with a short TCP check; only return ok:true if BOTH the exit code is 0 AND the port now answers. Exit 0 can mean 'command accepted' while the tunnel is still coming up or silently failed. Pair this with an in-flight guard (a `Map<action, Promise>` so two rapid clicks share one child process) to avoid racing duplicate mutations.

Why: gives the UI an honest green state and avoids the 'said on, actually off' class of bug. Related: [[Wiring an external MCP-serving CLI into a Next.js app status-on-provider, actions-on-dedicated-route]].

## Related

- [[3 Resources/Practices/Architecture/Wiring an external MCP-serving CLI into a Next.js app status-on-provider, actions-on-dedicated-route]]

%% ai-graph-start %%

**Related notes:**
- [[Wiring an external MCP-serving CLI into a Next.js app status-on-provider, actions-on-dedicated-route]]
- [[Polaris 3003 MCP server is persistent — TCP probe not equal to polaris tunnel state]]
- [[polaris-cli 0.2.0 operational gotchas (bootstrap scope, status, up, agents)]]
- [[polaris-cli never writes ~.polarisstate.json — no reliable bootstrapped signal]]
- [[Vinnstack auth providers two patterns and the rule for adding one]]

%% ai-graph-end %%