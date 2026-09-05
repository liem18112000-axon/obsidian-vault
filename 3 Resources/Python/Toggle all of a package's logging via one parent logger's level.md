---
title: "Toggle all of a package's logging via one parent logger's level"
created: 2026-08-27
type: howto
status: seedling
source: "session 2026-08-27 — kga monitoring.py"
tags: [logging, python, monitoring, gotcha, testing]
---

# Toggle all of a package's logging via one parent logger's level

To give a package a single on/off logging switch, put every logger under one parent name (`logging.getLogger("kga.loop")`, `"kga.atlassian"`, ...) so they are all children of `"kga"`. Configure ONLY the parent: attach one StreamHandler, set `propagate=False` (so records do not double-print through uvicorn/root), and control everything by the PARENT level.

Children created with default level `NOTSET` inherit the parent effective level, so one `logger.setLevel(...)` governs the whole package.

**Off = set the parent level ABOVE CRITICAL** (`logging.CRITICAL + 1`, i.e. 51). Do NOT use `logger.disabled = True` — `disabled` is per-logger and does not affect children, so child loggers would still emit. Raising the level silences every child including CRITICAL, with near-zero overhead when off (isEnabledFor short-circuits).

Read the toggle from env once on first `get_logger()` use (e.g. `KGA_LOG=1`, `KGA_LOG_LEVEL=INFO`) and also expose `enable()/disable()` for runtime flips.

**Testing gotcha:** pytest `caplog` captures via the ROOT logger, but `propagate=False` stops records reaching root — so caplog will not see them. Test instead by adding your own `logging.Handler` to the `"kga"` logger and asserting on captured messages, or assert `logging.getLogger("kga").isEnabledFor(level)`.

Context: kga `monitoring.py` (LUZ-159671 test-agent).

## Related

- [[Retry only transient HTTP failures]]
- [[and test backoff by mocking asyncio.sleep]]
