---
title: "FastAPI StaticFiles mount ordering and check_dir for optional local-only directories"
created: 2026-07-11
type: lesson
status: seedling
source: "virtual-avatar session 2026-07-11, app/main.py"
tags: [fastapi, starlette, staticfiles, routing, gotcha]
---

# FastAPI StaticFiles mount ordering and check_dir for optional local-only directories

Two FastAPI/Starlette `StaticFiles` gotchas that come up together when adding a second static mount alongside a catch-all one:

1. **Mount registration order matters.** Starlette matches mounted sub-applications in the order they were registered, not by specificity. If `app.mount("/", StaticFiles(directory="static"), ...)` is registered before a more specific `app.mount("/avatar-samples", ...)`, the catch-all `/` mount swallows every request first and the specific one becomes unreachable. Any specific-path mount must be registered *before* a catch-all `"/"` mount.

2. **`check_dir=False` for directories that may not exist at startup.** `StaticFiles(directory=...)` defaults to `check_dir=True`, which raises `RuntimeError` at app startup if the directory is missing. This breaks apps where a mount points at a directory that's optional/local-only (e.g., gitignored, populated by a separate download script, absent in a fresh clone or in a deployed container image). Passing `check_dir=False` lets the app start regardless; requests to a missing directory then just 404 naturally instead of crashing the whole process.

Both applied together in `app/main.py` of the virtual-avatar project: `app.mount("/avatar-samples", StaticFiles(directory="avatar-samples", check_dir=False), ...)` registered before the existing `app.mount("/", StaticFiles(directory="static"), ...)`, so a gitignored, dev-only avatar-samples folder can be served for local testing without breaking Cloud Run deployments where that folder does not exist.
