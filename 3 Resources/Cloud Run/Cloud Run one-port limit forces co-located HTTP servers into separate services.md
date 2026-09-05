---
title: "Cloud Run one-port limit forces co-located HTTP servers into separate services"
created: 2026-08-27
type: lesson
status: seedling
source: "session 2026-08-27"
tags: [cloud-run, gcp, architecture, deployment]
---

# Cloud Run one-port limit forces co-located HTTP servers into separate services

A Cloud Run **service exposes exactly one public port**. So when two HTTP servers logically sit together — e.g. an A2A agent and the MCP bridge that fronts it — you cannot publish both on the same service. Two clean shapes:

- **Separate services (preferred):** deploy the same image twice with different command + env — agent on its own URL, bridge pointing `KGA_A2A_URL` at that URL. One process per container (the Cloud Run best practice).
- **Single container:** the bridge is the public front on `$PORT`; the agent runs as an internal process on `localhost:8081`. One deployable, but needs a supervisor to run two processes.

Bundling `mcp` into the image (`pip install ".[bridge]"`) lets one image serve either role, chosen by the container command.

See [[MCP stdio transport cannot be hosted remotely; use Streamable HTTP]], [[Multi-turn agent sessions need min-instances=1 and session affinity on Cloud Run]].

## Related

- [[MCP stdio transport cannot be hosted remotely; use Streamable HTTP]]
- [[Multi-turn agent sessions need min-instances=1 and session affinity on Cloud Run]]
