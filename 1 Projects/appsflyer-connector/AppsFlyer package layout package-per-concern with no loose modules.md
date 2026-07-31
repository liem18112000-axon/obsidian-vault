---
title: "AppsFlyer package layout: package-per-concern with no loose modules"
created: 2026-07-03
type: lesson
status: seedling
source: "session 2026-07-03"
tags: [leo-cdp, appsflyer, refactor, package-layout]
---

# AppsFlyer package layout: package-per-concern with no loose modules

Decision (2026-07-03): appsflyer package now has NO loose modules — every concern is a package: `client/` (core.py = Pull API client, constants.py = MAX_ROWS/TS_FMT/window+rate-limit markers), `mapping/` (core.py = row→CdpEvent, transform.py, reports.py, constants.py = aliases/identity/property/time fields), `generator/` (gained constants.py = S2S consts + all synthetic-data fixtures incl. SHAPES/HEADERS; re-exports TS_FMT from client), `pipeline/` (gained cli.py = the leo-appsflyer entry point), `puller/`. The old top-level constants.py was split by concern rather than moved wholesale — TS_FMT is the one shared constant (client owns it, generator re-exports). Package `__init__` files re-export the public names so `from ..client import AppsFlyerClient` style imports keep working; pyproject entry point moved to `leo_connectors.appsflyer.pipeline.cli:main`. Push package + docs/examples/workflows were deleted by the user the same day; test_push.py, Dockerfile.push, and the leo-appsflyer-push entry point went with them.

Related: [[AppsFlyer connector reduced to a single JSONL file-S3 sink]]

## Related

- [[AppsFlyer connector reduced to a single JSONL file-S3 sink]]
