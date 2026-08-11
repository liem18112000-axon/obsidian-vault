---
ai_hash: 2cb027f43ff404c5
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-03
entities: []
source: session 2026-07-03 appsflyer-data-connector
status: seedling
tags:
- python
- testing
- monkeypatch
- gotcha
title: Monkeypatched module attributes are a hidden breakage risk when a module becomes
  a package
type: lesson
---

# Monkeypatched module attributes are a hidden breakage risk when a module becomes a package

Re-exporting names in a package `__init__.py` preserves \*imports\*, but NOT tests that monkeypatch module-level attributes. `monkeypatch.setattr('pkg.sink.post_with_retry', fake)` patched the name in the old `sink.py` namespace; after the split, the class actually calls the copy bound in `pkg.sink.cdp_http`, so the patch lands on the re-export and silently stops intercepting.

Before converting a module to a package, grep for patch sites, not just import sites:

```
rg "setattr.*sink|patch.*sink|sink\.(post_with_retry|requests)"
```

Fix options if hits exist: patch the submodule path (`pkg.sink.cdp_http.post_with_retry`), or better, inject the dependency (the sinks here take an injectable `session`/`producer`, which is why the appsflyer-data-connector split needed zero test changes).

Companion recipe: [[Convert a Python module to a package without breaking importers via re-exporting __init__]].

## Related

- [[Convert a Python module to a package without breaking importers via re-exporting __init__]]

**Confirmed in practice (http.py → http/ package):** string-form patches like `monkeypatch.setattr("pkg.http.time.sleep", ...)` break for a second reason — the attribute chain itself stops resolving, because the package `__init__.py` (unlike the old module) never imports `time`. Same for `setattr(http.requests, "post", ...)`. Working repoints: target the submodule that owns the import (`pkg.http.retry.time.sleep`) or patch the third-party module directly (`"requests.post"`) — both are behavior-identical since the originals mutated the global module objects anyway. Also surfaced: a caller importing another module's _private helpers should import them from the owning submodule after the split, not have the package re-export underscore names.

**Refinement (same repo, next day):** importing `_private` names from the owning submodule silences nothing — linters flag \*any\* cross-module access to a protected member (`Access to a protected member _is_retryable of a module`). The underscore was the bug, not the import path: if code in another package needs a helper, it is de facto public API — drop the underscore (`_backoff` → `backoff`, etc.), re-export it from the package `__init__`, and keep private only what stays inside its own module (`_expo`). Rule of thumb: a leading underscore is a promise that only this module uses it; the first external importer breaks the promise, so rename rather than import around it.

**Worst-case variant (hit on push.py → push/ split):** when the patched name is a *collaborator the code-under-test calls to avoid a side effect*, the miss does not error — it silently runs the real thing. `monkeypatch.setattr(push, "serve", fake)` patched the package attribute, but `push.cli.main` resolves `serve` from `push.cli`'s namespace → the REAL `ThreadingHTTPServer.serve_forever()` started and the test suite hung forever (no failure, no traceback — just a timeout). A hanging suite after a module→package split should immediately suggest: find every test that patches a *blocking* or *networked* collaborator through the old module namespace and repoint it to the submodule the caller actually imports from (`push.cli.serve`, `push.server.ThreadingHTTPServer`).

%% ai-graph-start %%

**Related notes:**
- [[Convert a Python module to a package without breaking importers via re-exporting __init__]]
- [[Extracting a shared utils package - classify by whether code knows source semantics]]
- [[AppsFlyer package layout package-per-concern with no loose modules]]
- [[Module-level load_dotenv lets unit tests hit real cloud credentials]]

%% ai-graph-end %%