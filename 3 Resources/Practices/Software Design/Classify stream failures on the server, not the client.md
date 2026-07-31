---
title: "Classify stream failures on the server, not the client"
created: 2026-07-01
type: lesson
status: seedling
source: "code review 2026-07-01 (vinnstack)"
tags: [streaming, ndjson, client-server, error-handling, coupling, retry]
---

# Classify stream failures on the server, not the client

When a server streams a child process's output to a browser over NDJSON, the **server** should classify a failure and emit ONE typed reason event (e.g. `{type:'failure', reached, reason}`) — rather than forwarding raw `stderr`/exit-code/`error` frames and letting the client stitch a human-readable cause together.

**Why:**
- **Coupling** — a client that reads three route-internal event types by string-keyed field access has no compile-time link to the route; renaming a field silently degrades it to a useless generic message. The server is also the only layer that *knows* why it failed (couldn't-launch/ENOENT vs ran-and-errored vs empty output vs terminated).
- **Staleness across a transparent retry** — this is the subtle one. If the server retries an attempt without telling the client (e.g. retry a stale session without `--resume`), any per-attempt state the client accumulates across the single stream (an `errDetail` buffer, usage/cost tallies) leaks the discarded attempt into the final result. The fix is to keep that state **server-side and reset it at the retry boundary**, so the emitted reason/usage reflect only the final attempt.

**Corollaries:**
- Distinguish 'couldn't reach/launch' (spawn error → reached:false) from 'ran but failed' (spawned + exited → reached:true) so the UI doesn't tell the user to reinstall a binary that ran fine. See [[Don't misattribute a failure's cause in the error message shown to users]].
- Gate usage/cost accounting on a NON-error result — a retried error attempt also emits a result event, and counting it double-charges spend.

Surfaced fixing vinnstack's chat route + ChatProvider (review Findings 5/6/7).

Related: [[3 Resources/AI/Agents/Keep errorfailure text out of LLM-summarized durable memory]].

## Related

- [[3 Resources/AI/Agents/Keep errorfailure text out of LLM-summarized durable memory]]
