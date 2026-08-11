---
ai_hash: 79aa273576d3223d
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-14
entities: []
source: session 2026-07-14
status: seedling
tags:
- architecture
- mcp
- nextjs
- integration
- polaris
- vinnstack
title: 'Wiring an external MCP-serving CLI into a Next.js app: status-on-provider,
  actions-on-dedicated-route'
type: lesson
---

# Wiring an external MCP-serving CLI into a Next.js app: status-on-provider, actions-on-dedicated-route

Design decision (Vinnstack Polaris integration plan, 2026-07-14): when embedding an external CLI that serves content over an MCP tunnel (e.g. Polaris), split the wiring along a clean seam:

- **Shared CLI shim** — one module (`lib/account/polarisCli.ts`) owns bin resolution, tunnel tcp-probe, the bounded `run()` spawn, and an in-flight `Map` guard so two concurrent identical actions (e.g. double-clicking 'Turn on') share one child process. Everything else calls the shim.
- **Status stays on the existing provider card; mutations + content get a dedicated route.** Reuse the already-cached `GET /api/auth/<provider>` for status (don't overload the generic provider route with provider-specific verbs like up/down/bootstrap). Put those under a new `/api/polaris/**` tree.
- **Cache reads, never mutations.** `createTtlCache` (60s) for list/status reads; up/down/bootstrap always run fresh and invalidate the cache after.
- **Degrade honestly with sentinels, not empty results.** Tunnel down → return a typed `{ tunnelDown: true }` so the UI can offer 'Turn on', instead of an empty list that reads as 'no content'. CLI absent → install hint. A config kill-switch (`polarisEnabled=false`) short-circuits every new path — the single rollback lever.
- **Guard the hot paths behind an opt-in.** Edits to the agent runner / system-prompt builder only fire when a Polaris agent is explicitly selected, so default behavior is byte-identical (zero regression) and provable by diffing the spawned argv.

Why: keeps the integration additive and reversible, avoids a second parallel status-polling path, and makes 'off' the safe default. Full plan: `doc/polaris-mcp-implementation-plan.md`.

## Related

- [[Vinnstack Polaris integration is three passive touchpoints]]
- [[3 Resources/Work-Side/Polaris/Polaris 0.2.0 serves agentsskillsrules over an MCP tunnel]]

%% ai-graph-start %%

**Related notes:**
- [[Vinnstack Polaris integration is three passive touchpoints]]
- [[Verify-after-mutate re-probe external state before reporting a CLI toggle succeeded]]
- [[Vinnstack auth providers two patterns and the rule for adding one]]
- [[Polaris 3003 MCP server is persistent — TCP probe not equal to polaris tunnel state]]
- [[Polaris 0.2.0 serves agentsskillsrules over an MCP tunnel]]

%% ai-graph-end %%