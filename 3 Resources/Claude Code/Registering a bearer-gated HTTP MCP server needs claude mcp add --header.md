---
title: "Registering a bearer-gated HTTP MCP server needs claude mcp add --header"
created: 2026-08-31
type: lesson
status: seedling
source: "session 2026-08-31 mcp re-register"
tags: [claude-code, mcp, auth, gotcha, http]
---

# Registering a bearer-gated HTTP MCP server needs claude mcp add --header

`claude mcp add --transport http <name> <url>` registers the server, but if the endpoint is auth-gated it will show **✘ Failed to connect** in `claude mcp list` (the server responds 401 to the handshake). You must pass the credential as a header:

  claude mcp add --transport http <name> <url> --header "Authorization: Bearer <token>"

Symptom vs cause: 'Failed to connect' on an HTTP MCP server that you know is up and healthy (curl returns 401, not a timeout/refused) = missing/wrong auth header, not a networking problem. Re-add WITH the header; `claude mcp list` then shows ✔ Connected. The header (token) is stored in .claude.json under the project — fetch it from your secret store (e.g. `gcloud secrets versions access latest --secret=...`) rather than pasting it, and don't echo it. Config changes take effect in the NEXT Claude Code session. Related: the test-agent A2A→MCP bridges gate /mcp with an inbound bearer secret; see [[Co-locating a stateful MCP bridge as an agent sidecar couples their scaling]].

## Related

- [[Co-locating a stateful MCP bridge as an agent sidecar couples their scaling]]
