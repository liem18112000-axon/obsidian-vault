---
title: "luz-docs 2026-06-11 dev integration run failure clusters"
created: 2026-06-11
type: observation
status: seedling
source: "Cloud Logging job 49c5c983, session 2026-06-11"
tags: [luz-docs, behave, integration-test, dev]
---

# luz-docs 2026-06-11 dev integration run failure clusters

The 2026-06-11 behave integration run against dev (tenant d0783310-d67f-4ab7-9aab-dcaef3f17f48) had 363/440 scenarios pass; the 77 failures collapse into a handful of backend regressions, not 77 separate problems:

1. **Search response missing `totalRecordCount`** — `POST /documents/search` returned 200 with a body containing only `results`; every step reading the count got `None` (~40 scenarios, including facets coming back empty). Likely tied to the excludeTotalCount/search-response work.
2. **`GET /documents` + any query param → 500** — `sort`, `folderId`, `includeDeletedDocs`, `skipSecurityClasses`, `excludeTotalCount` all 500'd (~9 scenarios).
3. **`POST /folders` intermittent 500 / non-JSON body** — folder creation failures cascaded into 404s when later steps fetched/moved those folders, hitting the security-class move/copy and LUZ-154157 rename-cascade scenarios (~12).
4. **Security-class materialization wrong** — patched `securityClassCodes` persisted as `[]`, `_isPublic` stayed `true` when it should flip `false`, folders with inherited security classes invisible in list/search (~8).
5. Noise: one Vault health check 429 (standby node), a few `POST /documents` 400s in setup steps.

Diagnostic heuristic: when a behave run shows dozens of failures, cluster by HTTP endpoint + status first — the count of *root causes* is usually far smaller than the count of red scenarios.

## Related

- [[Resolve Cloud Logging share links via redirect Location header]]
