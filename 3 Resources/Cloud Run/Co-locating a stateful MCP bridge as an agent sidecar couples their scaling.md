---
title: "Co-locating a stateful MCP bridge as an agent sidecar couples their scaling"
created: 2026-08-31
type: argument
status: seedling
source: "session 2026-08-31 test-agent"
tags: [cloud-run, sidecar, mcp, a2a, deployment, scaling, architecture]
---

# Co-locating a stateful MCP bridge as an agent sidecar couples their scaling

Cloud Run v2 supports multi-container **sidecars**, so an A2A→MCP bridge can be co-located with its agent in ONE service (bridge = ingress container serving /mcp; agent = sidecar reachable only on localhost) instead of two separate services.

**Benefits:** the agent stops being internet-exposed (only the MCP endpoint is), the bridge→agent hop becomes a localhost call (no public URL, no cross-service auth, lower latency), and you halve the service/SA/URL count with atomic same-revision deploys.

**The catch (the real decision): scaling gets coupled.** A bridge that keeps multi-turn session state in memory needs `session_affinity=true, min=max=1, cpu_idle=false` (pin to one warm instance). A stateless agent (rehydrates from a task store + object storage each turn) can scale. Put them in one service and ONE scaling policy governs both — the bridge's single-instance pin drags the agent to 1 instance too.

**Rule of thumb:** co-locate when the workload is low-concurrency (one client driving one pipeline) — it's a free security + simplicity win. Keep them split (or externalize the bridge's session map to a shared store first) when the agent must scale independently of the bridge. "One port per service" is NOT a blocker — with sidecars only the ingress container is exposed. Related: [[test-agent two A2A agents share a skeleton but diverge in domain engines]].

## Related

- [[test-agent two A2A agents share a skeleton but diverge in domain engines]]
