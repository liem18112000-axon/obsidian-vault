---
title: "Verify-after-mutate: re-probe external state before reporting a CLI toggle succeeded"
created: 2026-07-14
type: lesson
status: seedling
source: "session 2026-07-14"
tags: [cli, integration, reliability, polaris, pattern]
---

# Verify-after-mutate: re-probe external state before reporting a CLI toggle succeeded

When an app wraps a CLI that toggles external state (e.g. `polaris up` starting an MCP tunnel), don't trust the command's exit code alone — **verify the effect before reporting success**. Pattern used in Vinnstack's `polarisCli.tunnelUp()`: run `polaris up`, then re-probe the port with a short TCP check; only return ok:true if BOTH the exit code is 0 AND the port now answers. Exit 0 can mean 'command accepted' while the tunnel is still coming up or silently failed. Pair this with an in-flight guard (a `Map<action, Promise>` so two rapid clicks share one child process) to avoid racing duplicate mutations.

Why: gives the UI an honest green state and avoids the 'said on, actually off' class of bug. Related: [[Wiring an external MCP-serving CLI into a Next.js app status-on-provider, actions-on-dedicated-route]].

## Related

- [[3 Resources/Practices/Architecture/Wiring an external MCP-serving CLI into a Next.js app status-on-provider, actions-on-dedicated-route]]
