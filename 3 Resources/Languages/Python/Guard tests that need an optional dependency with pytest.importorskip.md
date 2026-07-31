---
ai_hash: 4314d334ec815a28
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-15
entities: []
source: session 2026-06-15, accesstrade_integration CI failure
status: seedling
tags:
- pytest
- testing
- optional-dependency
- ci-cd
- gotcha
title: Guard tests that need an optional dependency with pytest.importorskip
type: lesson
---

# Guard tests that need an optional dependency with pytest.importorskip

**A test that depends on an OPTIONAL dependency (one behind an extra, e.g. `.[redis]`) will pass on your machine — where you pip-installed it — but FAIL in CI that installs a leaner extras set (e.g. `.[web,dev]`). Guard such tests with `pytest.importorskip("redis")` so they skip (not fail) when the lib is absent, AND add the extra to the CI matrix you want to actually exercise.**

Concrete: `test_services_uses_redis_when_url_set` asserted `Services(...).link_cache` is `RedisLinkCache` when `REDIS_URL` is set. Locally green (I'd `pip install redis`); in CI it fell back to SQLite (`No module named 'redis'`) → AssertionError. Two-part fix:
1. Split the test: the SQLite-FALLBACK assertion runs always; the redis-backed assertion is gated by `pytest.importorskip("redis")`.
2. Make CI install the extra (`pip install .[web,dev,redis]`) so the path is genuinely covered, not silently skipped.

General rules:
- Don't write tests whose result depends on what happens to be installed in the dev venv. Either guard with importorskip or install the dep in CI (ideally both — importorskip for robustness, the extra in CI so it doesn't no-op).
- Mirror the runtime contract: if code FALLS BACK when a lib is missing, test the fallback unconditionally and the enabled path under importorskip.
- Catch this class of bug by running tests in the SAME extras set CI uses, not your fully-loaded venv. Relates to [[An optimization-only cache should fail soft, never raise on backend errors]].

%% ai-graph-start %%

**Related notes:**
- [[pytest imports all test modules before applying -m deselection]]
- [[An optimization-only cache should fail soft, never raise on backend errors]]
- [[Black-box exe test suite skips silently when no artifact is present]]

%% ai-graph-end %%