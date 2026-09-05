---
title: "SQLAlchemy URL.create encodes passwords correctly; hand-encoding with quote_plus can corrupt them"
created: 2026-08-29
type: gotcha
status: seedling
source: "session 2026-08-29 task-store migration"
tags: [sqlalchemy, url-encoding, gotcha, python]
---

# SQLAlchemy URL.create encodes passwords correctly; hand-encoding with quote_plus can corrupt them

When building a SQLAlchemy database URL, prefer `sqlalchemy.URL.create(...)` with raw `username`/`password`/`query` values over hand-assembling the string — it percent-encodes each part with the *correct* codec for its position.

The gotcha with hand-encoding: SQLAlchemy decodes the **netloc password** with `urllib.parse.unquote` (percent-only), **not** `unquote_plus`. So if you encode the password with `quote_plus`, a literal space becomes `+`, which `unquote` leaves as a literal `+` — silently corrupting the password (a `+` in the input is fine because `quote_plus` maps it to `%2B`; only spaces break). Query values have their own decoding rules too. Delegating to `URL.create` sidesteps all of this.

```python
# fragile:
f"postgresql+asyncpg://{quote_plus(user)}:{quote_plus(pw)}@/{db}?host={quote_plus(sock)}"
# robust:
URL.create("postgresql+asyncpg", username=user, password=pw, database=db, query={"host": sock})
```

Verify a round-trip with `engine.url.password == pw` and log safely with `engine.url.render_as_string(hide_password=True)`.

Related: [[Cloud Run to Cloud SQL via Auth-proxy unix socket with asyncpg]].

## Related

- [[Cloud Run to Cloud SQL via Auth-proxy unix socket with asyncpg]]
