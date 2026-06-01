---
title: "Flat-import Python modules can be relocated together without rewriting imports"
created: 2026-06-01
type: lesson
status: seedling
source: "vault-graph refactor 2026-06-01"
tags: [python, imports, refactor, docker]
---

# Flat-import Python modules can be relocated together without rewriting imports

Modules that use **flat imports** — `import vertex_client`, `from proxy import app` — rather than package-qualified ones (`from mypkg.proxy import app`) resolve siblings **by directory on `sys.path`**, not by package. So you can move a whole set of such modules together into a new subdirectory (e.g. `src/`) **without editing a single import statement**, as long as that directory ends up on `sys.path`.

Two conditions make it work:

1. **Locally**, run the entrypoint from *inside* the new directory so it lands on `sys.path`: `cd src && uvicorn server:app`. Running `uvicorn src.server:app` from the parent would instead require the modules to be package-qualified.
2. **In Docker**, COPY only that subdir's *contents* flat into the workdir so the modules stay siblings:
   ```dockerfile
   COPY tools/vault-graph/src/ /app/   # not COPY tools/vault-graph/ /app/
   ```
   Then `WORKDIR /app` + `uvicorn server:app` still resolves every flat import.

This is why a "move all `*.py` into `src/`" refactor can be import-edit-free. Watch the `__file__`-relative path gotcha though — see [[Path(__file__).parent breaks when a module is moved to a deeper directory]].

## Related

- [[Path(__file__).parent breaks when a module is moved to a deeper directory]]
