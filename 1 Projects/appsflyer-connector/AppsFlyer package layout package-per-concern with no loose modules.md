---
ai_hash: 153d925520a85dc4
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-03
entities:
- appsflyer package
- package-per-concern
- no loose modules
- client/
- mapping/
- generator/
- pipeline/
- puller/
- client/core.py
- client/constants.py
- Pull API client
- MAX_ROWS
- TS_FMT
- window+rate-limit markers
- mapping/core.py
- mapping/transform.py
- mapping/reports.py
- mapping/constants.py
- row→CdpEvent
- aliases
- identity fields
- property fields
- time fields
- generator/constants.py
- S2S consts
- synthetic-data fixtures
- SHAPES
- HEADERS
- pipeline/cli.py
- leo-appsflyer entry point
- old top-level constants.py
- shared constant
- __init__ files
- public names
- pyproject entry point
- leo_connectors.appsflyer.pipeline.cli:main
- Push package
- docs/examples/workflows
- test_push.py
- Dockerfile.push
- leo-appsflyer-push entry point
- AppsFlyer connector reduced to a single JSONL file-S3 sink
- '2026-07-03'
- AppsFlyer package layout
source: session 2026-07-03
status: seedling
tags:
- leo-cdp
- appsflyer
- refactor
- package-layout
title: 'AppsFlyer package layout: package-per-concern with no loose modules'
type: lesson
---

# AppsFlyer package layout: package-per-concern with no loose modules

Decision (2026-07-03): appsflyer package now has NO loose modules — every concern is a package: `client/` (core.py = Pull API client, constants.py = MAX_ROWS/TS_FMT/window+rate-limit markers), `mapping/` (core.py = row→CdpEvent, transform.py, reports.py, constants.py = aliases/identity/property/time fields), `generator/` (gained constants.py = S2S consts + all synthetic-data fixtures incl. SHAPES/HEADERS; re-exports TS_FMT from client), `pipeline/` (gained cli.py = the leo-appsflyer entry point), `puller/`. The old top-level constants.py was split by concern rather than moved wholesale — TS_FMT is the one shared constant (client owns it, generator re-exports). Package `__init__` files re-export the public names so `from ..client import AppsFlyerClient` style imports keep working; pyproject entry point moved to `leo_connectors.appsflyer.pipeline.cli:main`. Push package + docs/examples/workflows were deleted by the user the same day; test_push.py, Dockerfile.push, and the leo-appsflyer-push entry point went with them.

Related: [[AppsFlyer connector reduced to a single JSONL file-S3 sink]]

## Related

- [[AppsFlyer connector reduced to a single JSONL file-S3 sink]]

%% ai-graph-start %%

**Related notes:**
- [[AppsFlyer connector dropped Kafka sink and S3 raw landing]]
- [[AppsFlyer connector reduced to a single JSONL file-S3 sink]]
- [[Two Dockerfiles differing only in entrypoint should be one image plus compose override]]
- [[AppsFlyer Push layer appends per-event while Pull replaces the day]]
- [[Extracting a shared utils package - classify by whether code knows source semantics]]

**Relations:**
- appsflyer package — *adopted layout* — package-per-concern
- appsflyer package — *adopted layout* — no loose modules
- appsflyer package — *contains* — client/
- appsflyer package — *contains* — mapping/
- appsflyer package — *contains* — generator/
- appsflyer package — *contains* — pipeline/
- appsflyer package — *contains* — puller/
- client/ — *includes module* — client/core.py
- client/ — *includes module* — client/constants.py
- client/core.py — *functions as* — Pull API client
- client/constants.py — *defines* — MAX_ROWS
- client/constants.py — *defines* — TS_FMT
- client/constants.py — *defines* — window+rate-limit markers
- mapping/ — *includes module* — mapping/core.py
- mapping/ — *includes module* — mapping/transform.py
- mapping/ — *includes module* — mapping/reports.py
- mapping/ — *includes module* — mapping/constants.py
- mapping/core.py — *transforms* — row→CdpEvent
- mapping/constants.py — *defines* — aliases
- mapping/constants.py — *defines* — identity fields
- mapping/constants.py — *defines* — property fields
- mapping/constants.py — *defines* — time fields
- generator/ — *gained module* — generator/constants.py
- generator/constants.py — *defines* — S2S consts
- generator/constants.py — *defines* — synthetic-data fixtures
- synthetic-data fixtures — *includes* — SHAPES
- synthetic-data fixtures — *includes* — HEADERS
- generator/ — *re-exports* — TS_FMT
- TS_FMT — *owned by* — client/constants.py
- pipeline/ — *gained module* — pipeline/cli.py
- pipeline/cli.py — *is* — leo-appsflyer entry point
- old top-level constants.py — *was split by* — concern
- TS_FMT — *is a* — shared constant
- __init__ files — *re-export* — public names
- pyproject entry point — *moved to* — leo_connectors.appsflyer.pipeline.cli:main
- Push package — *deleted on* — 2026-07-03
- docs/examples/workflows — *deleted on* — 2026-07-03
- test_push.py — *deleted on* — 2026-07-03
- Dockerfile.push — *deleted on* — 2026-07-03
- leo-appsflyer-push entry point — *deleted on* — 2026-07-03
- AppsFlyer package layout — *is related to* — AppsFlyer connector reduced to a single JSONL file-S3 sink

%% ai-graph-end %%