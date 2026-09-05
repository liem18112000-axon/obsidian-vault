---
title: "Tell FastAPI from bare Starlette by the 404 body — {\"detail\":\"Not Found\"} vs plain Not Found"
created: 2026-08-28
type: howto
status: seedling
source: "session 2026-08-28"
tags: [fastapi, starlette, asgi, verification, howto]
---

# Tell FastAPI from bare Starlette by the 404 body — {"detail":"Not Found"} vs plain Not Found

To confirm from OUTSIDE that a deployed ASGI service is running **FastAPI** and not bare **Starlette**, hit a path that does not exist and look at the 404 body:

- FastAPI installs a default exception handler that returns JSON: `{"detail":"Not Found"}` (content-type application/json).
- Bare Starlette (routes assembled directly, no FastAPI) returns plain text `Not Found` (text/plain).

So `curl https://svc/__nope__` distinguishes them with no access to the code. Caveat: if the app has an auth middleware in front, an unauthenticated request is answered by the MIDDLEWARE (e.g. 401) before routing reaches the 404 handler — send a VALID credential so the request passes the middleware and actually reaches FastAPIs router, then read the 404 body.

Used to verify a Cloud Run redeploy: FastAPI 404 `{"detail":"Not Found"}` (with bearer) + the pinned image digest matching the just-built one + a live `message/send` returning a sensible reply = three independent proofs the new FastAPI revision is the one serving.

See [[Cloud Run latest does not roll a new revision on terraform apply — deploy by digest]].

## Related

- [[Cloud Run latest does not roll a new revision on terraform apply — deploy by digest]]
