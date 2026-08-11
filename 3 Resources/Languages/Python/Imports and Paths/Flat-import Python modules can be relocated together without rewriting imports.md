---
ai_hash: d5bec1a17905c34c
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-01
entities: []
source: vault-graph refactor 2026-06-01
status: seedling
tags:
- python
- imports
- refactor
- docker
title: Flat-import Python modules can be relocated together without rewriting imports
type: lesson
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

%% ai-graph-start %%

**Related notes:**
- [[Path(__file__).parent breaks when a module is moved to a deeper directory]]
- [[pip install does not bundle templatesstatic referenced relative to a package]]
- [[Convert a Python module to a package without breaking importers via re-exporting __init__]]
- [[Docker Compose path resolution env_file vs build context vs dockerfile]]
- [[Relocating docker-compose.yml renames the Compose project and orphans volumes]]

%% ai-graph-end %%