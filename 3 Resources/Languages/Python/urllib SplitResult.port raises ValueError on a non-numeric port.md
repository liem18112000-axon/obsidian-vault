---
title: "urllib SplitResult.port raises ValueError on a non-numeric port"
created: 2026-06-15
type: lesson
status: seedling
source: "session 2026-06-14, Copilot PR #3 review"
tags: [python, urllib, parsing, gotcha, validation]
---

# urllib SplitResult.port raises ValueError on a non-numeric port

**`urllib.parse.urlsplit(url).port` is a property that PARSES the port from the netloc and raises `ValueError` when it's non-numeric (e.g. `host:abc`) — it does not return None. So `port = parts.port or default` can throw before the `or` ever runs, crashing on a malformed URL.**

Guard it when the URL is untrusted/user-configured:
```python
try:
    explicit_port = parts.port
except ValueError:
    explicit_port = None
port = explicit_port or DEFAULT_PORTS.get(parts.scheme.lower())
```
(`.hostname` does not raise — only `.port`.) Found by Copilot on the admin panel: a bad `DATABASE_URL`/`REDIS_URL` would 500 the page instead of degrading to 'no host/port'. General rule: anything parsing an externally-supplied connection string should treat a malformed value as a soft failure, not an exception. Relates to [[Password-gate a server-rendered admin panel and show dependency-free store status]].
