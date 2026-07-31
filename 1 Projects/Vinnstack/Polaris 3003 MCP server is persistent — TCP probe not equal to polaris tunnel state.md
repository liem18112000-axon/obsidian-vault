---
title: "Polaris :3003 MCP server is persistent — TCP probe not equal to polaris tunnel state"
created: 2026-07-14
type: observation
status: seedling
source: "session 2026-07-14 PR-B verify"
tags: [polaris, mcp, gotcha, verification, vinnstack]
---

# Polaris :3003 MCP server is persistent — TCP probe not equal to polaris tunnel state

Verification finding (2026-07-14, Vinnstack PR-B): the app decides 'Polaris tunnel on/off' with a raw TCP probe of 127.0.0.1:3003 (polarisCli.polarisStatus → tcpReachable). But in the observed environment the **MCP HTTP server on :3003 is persistent and independent of `polaris up`/`polaris down`**. Proof: with `polaris status` reporting the tunnel OFF, :3003 stayed OPEN for 12s+, `POST /mcp initialize` returned HTTP 200, and /api/polaris/skills?q=gcloud still returned 19 items.

Consequence: after clicking 'Turn off', `polaris down` DOES run (CLI confirms off) but the app's indicator stays 'on' because the port keeps listening → the card looks like Turn-off failed. The probe actually measures 'is the MCP endpoint reachable' (which is what PR-C search / PR-D tool-calls truly need), NOT 'polaris tunnel state per `polaris status`'.

Fix options: (A) parse `polaris status` for authoritative tunnel state; (B, recommended) relabel the indicator to 'Reachable/MCP reachable' since reachability is the functional truth; (A+B) show both lines.

Also: Vinnstack config is process-cached (config.ts read() memoizes), so `polarisEnabled=false` and any config.json change needs an app restart to take effect. Related: [[Verify-after-mutate re-probe external state before reporting a CLI toggle succeeded]], [[Polaris MCP is search-only 5 tools, no list-all, driven over HTTP JSON-RPC not the CLI]].

## Related

- [[Verify-after-mutate re-probe external state before reporting a CLI toggle succeeded]]
