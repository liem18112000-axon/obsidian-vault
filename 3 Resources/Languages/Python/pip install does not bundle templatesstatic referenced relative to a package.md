---
ai_hash: aa9d2212c4cdff7e
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-14
entities: []
source: session 2026-06-14, accesstrade_integration Dockerfile
status: seedling
tags:
- python
- docker
- packaging
- fastapi
- gotcha
title: pip install does not bundle templates/static referenced relative to a package
type: gotcha
---

# pip install does not bundle templates/static referenced relative to a package

**A `pip install .` of a Python package does NOT include top-level `templates/` or `static/` directories that live OUTSIDE the import packages — so code that locates them via `Path(__file__).resolve().parent.parent / "templates"` breaks when the app runs from site-packages, because `parent.parent` then points into site-packages where those dirs don't exist.** Only modules inside the `packages.find` set (plus declared package-data) get installed.

Two ways out:
1. **Run from the copied source, not the installed package** (what I did for the Accesstrade web image): `COPY . /app`, `pip install .[web]` for deps, set `ENV PYTHONPATH=/app`, and run `python -m uvicorn api.app:app`. PYTHONPATH=/app is prepended before site-packages, so `import api` resolves to `/app/api`, `__file__` is under `/app`, and `/app/templates` + `/app/static` exist. (Verified: the container served `/` 200 and `/api/health` 200.)
2. **Make them real package data** — move templates/static inside a package and load via `importlib.resources`, or declare them in `[tool.setuptools.package-data]` / MANIFEST. More correct for a published wheel, more setup.

For a container you control, option 1 is simplest and keeps the `parent.parent` lookup working. The redundant package copy in site-packages is harmless because PYTHONPATH shadows it. Relates to [[Expose an app as an MCP server by wrapping the same services container the webCLI use]] (same repo).

%% ai-graph-start %%

**Related notes:**
- [[Flat-import Python modules can be relocated together without rewriting imports]]
- [[Docker Compose path resolution env_file vs build context vs dockerfile]]
- [[FastAPI StaticFiles mount ordering and check_dir for optional local-only directories]]
- [[Relocating docker-compose.yml renames the Compose project and orphans volumes]]
- [[Expose an app as an MCP server by wrapping the same services container the webCLI use]]

%% ai-graph-end %%