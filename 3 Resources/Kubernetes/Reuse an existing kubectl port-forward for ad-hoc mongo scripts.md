---
title: "Reuse an existing kubectl port-forward for ad-hoc mongo scripts"
created: 2026-07-13
type: lesson
status: seedling
source: "eArchive ops session 2026-07-13"
tags: [kubernetes, mongodb, kubectl, port-forward]
---

# Reuse an existing kubectl port-forward for ad-hoc mongo scripts

When a script (e.g. an ongoing seed job) already holds a `kubectl port-forward` open to a mongo replica-set pod, a second ad-hoc script can connect through the *same* local port concurrently — no need to open another port-forward.

Just point the new script's connection string at `mongodb://user:pass@localhost:<samePort>/db?directConnection=true` and run it alongside. MongoDB itself handles concurrent connections fine; the only shared resource is the local TCP port, which multiple client connections can share without conflict (only one *port-forward process* is needed, not one per script).

Useful for quick admin tasks (drop an index, check a count, inspect a doc) against a tenant DB while a long-running seed/prepare job is still using the tunnel, without juggling a second local port.

## Related

- [[Luz eArchive tenant mongo database collection list]]
