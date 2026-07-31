---
title: "An optimization-only cache should fail soft, never raise on backend errors"
created: 2026-06-14
type: lesson
status: seedling
source: "session 2026-06-14, accesstrade_integration RedisLinkCache"
tags: [caching, redis, resilience, protocol, optional-dependency, python]
---

# An optimization-only cache should fail soft, never raise on backend errors

**A cache that exists only as an optimization — and is read BEFORE the real work — must fail soft: on any backend error, `get` returns a miss (None) and `set` is a no-op, never an exception. Otherwise a transient cache outage breaks the actual operation it was meant to speed up.**

Concrete: `RedisLinkCache` (Accesstrade link-mint idempotency cache). Callers check the cache, then call the API only on a miss. So a Redis blip must look like a miss, not crash minting:
```python
def get(self, key):
    try: raw = self._client.get(self._prefix + key)
    except Exception as exc:   # fail soft -> miss
        logger.warning("redis get failed (%s); treating as miss", exc); return None
    return json.loads(raw) if raw else None
def set(self, key, value):
    try: self._client.set(self._prefix + key, json.dumps(value), ex=self._ttl)
    except Exception as exc:   # best-effort -> no-op
        logger.warning("redis set failed (%s); not cached", exc)
```
A broad `except Exception` is justified HERE precisely because the contract is 'never let the cache layer break the caller' — log and continue. (Contrast a source-of-truth store, where you must NOT swallow errors.)

Supporting patterns that made it clean:
- **Pluggable via a Protocol**: `LinkCache` = `get(key)->dict|None` / `set(key,value)`. Implementations (InMemory, JsonFile, SQLite repo, Redis) are interchangeable; a runtime selector picks one from config (`REDIS_URL` set → Redis, else SQLite), with a fallback if the optional lib is missing.
- **Lazy optional-dependency import**: `import redis` happens inside `__init__` only when a real client is built, so the class is importable (and unit-testable with an injected fake client) without the `redis` package installed. Ship it behind an extra (`pip install .[redis]`).
- **Test with an injected fake client** (a tiny object with get/set) — no real server, no fakeredis dep; add one `_BrokenRedis` that raises to prove fail-soft.

Relates to [[Idempotent link minting with content-hash cache keys]] and [[Docker hostname for reaching a service depends on where the caller runs]].
