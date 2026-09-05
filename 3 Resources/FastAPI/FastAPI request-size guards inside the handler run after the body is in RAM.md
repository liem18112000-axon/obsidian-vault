---
title: "FastAPI request-size guards inside the handler run after the body is in RAM"
created: 2026-09-05
type: lesson
status: seedling
source: "c360 python code review 2026-09-05"
tags: [fastapi, pydantic, dos, ingestion, gotcha]
---

# FastAPI request-size guards inside the handler run after the body is in RAM

A size/quantity guard written inside a FastAPI handler (e.g. `if len(payload.events) > N: raise 413`) runs only *after* Pydantic has already parsed and materialized the entire request body in memory. It cannot prevent the OOM it appears to guard against.

Enforce a request-body-size cap at the server/ingress layer (uvicorn/Starlette limit, or the reverse proxy) so oversized bodies are rejected before they are buffered. The in-handler count check is still useful for a clean 413, but it is not a memory-safety control.

## Related

- [[Pydantic min_length on a nullable str rejects empty strings]]
