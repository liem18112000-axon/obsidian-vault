---
title: "Bridge/gateway: use separate secrets for the inbound caller token and the outbound backend token"
created: 2026-08-28
type: lesson
status: seedling
source: "session 2026-08-27"
tags: [security, auth, bridge, secrets, design]
---

# Bridge/gateway: use separate secrets for the inbound caller token and the outbound backend token

A protocol bridge / gateway sits on two hops — it authenticates the CALLER coming in, and authenticates ITSELF to the downstream service going out. Use a DIFFERENT secret for each hop, never one shared token.

Why: if the two hops share a token, anyone who can call the bridge already holds the exact credential the bridge uses against the backend (and vice-versa) — compromising one hop leaks the other. Separate secrets contain the blast radius, let each hop rotate independently, and let you scope who-can-call-the-bridge apart from what-the-bridge-can-reach.

Concretely, for the A2A->MCP bridge: `KGA_BRIDGE_BEARER_TOKEN` (inbound, what Claude presents to the bridge) is its own Secret Manager secret, distinct from `A2A_BEARER_TOKEN` (outbound, what the bridge presents to the agent). Verified by the split: the old A2A token now returns 401 at the bridge while the new bridge token returns 400. A shared-token version is a convenient shortcut worth taking only for a throwaway/dev setup.

See [[A2A-to-MCP bridge is an MCP stdio server that is also an A2A client]], [[Cloud Run service-to-service with an app bearer needs the callee public (Authorization header collision)]].

## Related

- [[A2A-to-MCP bridge is an MCP stdio server that is also an A2A client]]
- [[Cloud Run service-to-service with an app bearer needs the callee public (Authorization header collision)]]
