---
title: "Windows refuses dead localhost ports slowly — probe 127.0.0.1, expect ~2s"
created: 2026-06-11
type: lesson
status: evergreen
source: "fb-info-project measurement 2026-06-11"
tags: [windows, networking, latency, gotcha, ollama]
---

# Windows refuses dead localhost ports slowly — probe 127.0.0.1, expect ~2s

On Windows, a TCP connect to a dead local port is **not** instant even though the OS answers with RST: winsock retries the refused connect internally (~2 s total), and connecting to `localhost` roughly doubles that (~4 s) because the IPv6 address (`::1`) is tried and refused before IPv4. Measured with urllib against a closed port: `localhost` 4.07 s, `127.0.0.1` 2.08 s.

Practical rules for probing local services (Ollama, dev servers, sidecars):
- default to `http://127.0.0.1:<port>`, not `http://localhost:<port>`;
- budget ~2 s for the failure case in any 'is it up?' check on Windows — a connect timeout shorter than that changes nothing because the time is spent in refused-connect retries, not waiting.

## Related

- [[Self-healing scraper selectors — LLM fallback only on verified failure]]
- [[then cache]]
