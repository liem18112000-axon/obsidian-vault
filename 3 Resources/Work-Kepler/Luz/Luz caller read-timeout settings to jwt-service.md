---
title: "Luz caller read-timeout settings to jwt-service"
created: 2026-06-30
type: observation
status: seedling
source: "PROD jwt-service investigation 2026-06-30"
tags: [luz, jwt-service, timeout, prod]
---

# Luz caller read-timeout settings to jwt-service

Observed HTTP **read-timeout** settings of Luz services that call [[jwt-service token endpoints and replicas (Luz prod)]], inferred from the round Istio `DC` latencies when jwt-service was slow:

| Caller | Read timeout |
| --- | --- |
| `luz-eletter` | ~30s |
| `luz-eletter-dispatcher` | ~30s |
| `luz-mylife-epost-adapter` | ~70s |

Other services seen taking read timeouts against jwt-service during a slowdown: `luz-store`, `luz-docs-view-controller`, `xent-rest`, `luzfin-finance`, `luz-retention`, `kie` (exact timeout values not yet confirmed for these).

**Why it matters:** when jwt-service exceeds these per-caller timeouts, the caller throws `SocketTimeoutException: Read timed out` and aborts (Istio logs `DC`). The eletter/epost callers look like async/batch dispatchers, so impact there is delayed jobs rather than interactive users. Technique: [[Istio DC response_flag with round latency = caller read timeout]].

## Related

- [[jwt-service token endpoints and replicas (Luz prod)]]
- [[Istio DC response_flag with round latency = caller read timeout]]
