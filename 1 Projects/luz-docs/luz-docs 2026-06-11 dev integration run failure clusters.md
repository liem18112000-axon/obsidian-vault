---
ai_hash: b8872d4f15b09fa8
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-11
entities:
- luz-docs
- '2026-06-11'
- dev integration run
- dev tenant
- d0783310-d67f-4ab7-9aab-dcaef3f17f48
- behave integration run
- scenarios
- failures
- backend regressions
- Search response missing totalRecordCount
- '`POST /documents/search`'
- Search response
- '`totalRecordCount`'
- '`results`'
- '`None`'
- steps reading count
- facets
- excludeTotalCount/search-response work
- '`GET /documents`'
- query param
- '`sort`'
- '`folderId`'
- '`includeDeletedDocs`'
- '`skipSecurityClasses`'
- '`excludeTotalCount`'
- '`POST /folders`'
- 500 (HTTP status)
- non-JSON body
- folder creation failures
- 404 (HTTP status)
- security-class move/copy scenarios
- LUZ-154157 rename-cascade scenarios
- Security-class materialization wrong
- '`securityClassCodes`'
- '`[]`'
- '`_isPublic`'
- '`true`'
- '`false`'
- folders
- inherited security classes
- list/search
- Vault health check 429
- standby node
- '`POST /documents`'
- 400 (HTTP status)
- setup steps
- HTTP endpoint
- status
- root causes
- Cloud Logging share links
- redirect Location header
source: Cloud Logging job 49c5c983, session 2026-06-11
status: seedling
tags:
- luz-docs
- behave
- integration-test
- dev
title: luz-docs 2026-06-11 dev integration run failure clusters
type: observation
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

%% ai-graph-start %%

**Related notes:**
- [[dev-staging luz-docs IT failures cluster on the materialize read-path]]
- [[luz-docs IT $SCn authorization failures were deterministic resolution regressions, not membership]]
- [[Luz delete-folder tests can only delete public folders, not ones carrying a security class]]
- [[luz-docs IT create-document step swallows server 500 and surfaces as cryptic base_metadata AttributeError]]
- [[JSON-driven Scenario Outline pattern for luz_docs materialize integration tests]]

**Relations:**
- luz-docs — *HAD_EVENT* — dev integration run
- dev integration run — *OCCURRED_ON* — 2026-06-11
- dev integration run — *TARGETED* — dev tenant
- dev tenant — *HAS_ID* — d0783310-d67f-4ab7-9aab-dcaef3f17f48
- dev integration run — *IS_A* — behave integration run
- behave integration run — *TOTAL_SCENARIOS* — 440
- behave integration run — *PASSED_SCENARIOS* — 363
- behave integration run — *FAILED_SCENARIOS* — 77
- failures — *ARE_CLASSIFIED_AS* — backend regressions
- backend regressions — *INCLUDE* — Search response missing totalRecordCount
- backend regressions — *INCLUDE* — `GET /documents` + any query param → 500
- backend regressions — *INCLUDE* — `POST /folders` intermittent 500 / non-JSON body
- backend regressions — *INCLUDE* — Security-class materialization wrong
- backend regressions — *INCLUDE* — Vault health check 429
- backend regressions — *INCLUDE* — `POST /documents` 400s
- Search response missing totalRecordCount — *AFFECTS_ENDPOINT* — `POST /documents/search`
- `POST /documents/search` — *RETURNED_STATUS* — 200 (HTTP status)
- `POST /documents/search` — *RESPONSE_BODY_CONTAINED* — `results`
- `totalRecordCount` — *WAS_MISSING_FROM* — Search response
- steps reading count — *GOT_VALUE* — `None`
- facets — *CAME_BACK* — empty
- Search response missing totalRecordCount — *TIED_TO* — excludeTotalCount/search-response work
- `GET /documents` — *WITH_QUERY_PARAM_CAUSES* — 500 (HTTP status)
- query param — *EXAMPLE* — `sort`
- query param — *EXAMPLE* — `folderId`
- query param — *EXAMPLE* — `includeDeletedDocs`
- query param — *EXAMPLE* — `skipSecurityClasses`
- query param — *EXAMPLE* — `excludeTotalCount`
- `POST /folders` — *HAD_ISSUE* — intermittent 500
- `POST /folders` — *HAD_ISSUE* — non-JSON body
- folder creation failures — *CAUSED_BY* — `POST /folders` intermittent 500 / non-JSON body
- folder creation failures — *CASCADED_INTO* — 404 (HTTP status)
- 404 (HTTP status) — *AFFECTED* — security-class move/copy scenarios
- 404 (HTTP status) — *AFFECTED* — LUZ-154157 rename-cascade scenarios
- Security-class materialization wrong — *INVOLVES_FIELD* — `securityClassCodes`
- `securityClassCodes` — *PERSISTED_AS* — `[]`
- Security-class materialization wrong — *INVOLVES_FIELD* — `_isPublic`
- `_isPublic` — *STAYED_AS* — `true`
- `_isPublic` — *SHOULD_FLIP_TO* — `false`
- folders — *WITH_PROPERTY* — inherited security classes
- folders — *WITH_INHERITED_SECURITY_CLASSES_ARE* — invisible in list/search
- Vault health check 429 — *OCCURRED_ON* — standby node
- `POST /documents` — *HAD_STATUS* — 400 (HTTP status)
- `POST /documents` 400s — *OCCURRED_IN* — setup steps
- behave run failures — *RECOMMENDED_CLUSTER_BY* — HTTP endpoint
- behave run failures — *RECOMMENDED_CLUSTER_BY* — status
- luz-docs — *RELATED_TO* — Cloud Logging share links
- Cloud Logging share links — *RESOLVED_VIA* — redirect Location header

%% ai-graph-end %%