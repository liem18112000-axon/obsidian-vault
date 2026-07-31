---
title: "AppsFlyer raw-data Pull API is plan-gated - 400 subscription error"
created: 2026-07-03
type: gotcha
status: seedling
source: "session 2026-07-03"
tags: [appsflyer, pull-api, subscription, http-400]
---

# AppsFlyer raw-data Pull API is plan-gated - 400 subscription error

Gotcha (2026-07-03): the AppsFlyer Pull API can fail with **HTTP 400 "Your current subscription package does not include raw data reports"** — a plan-level rejection, not a request error. Raw-data report access (installs_report, in_app_events_report, …) is a paid AppsFlyer feature; when a trial lapses or the plan changes, every pull starts 400ing even though the token is valid and the same call worked before. Diagnose by calling the endpoint directly and reading the 400 body (the connector's safe_detail hides bodies as "?" — re-issue the request with requests to see the message). No code fix: either upgrade the plan or stub the extract layer (inject a fake client into run_day) so the rest of the pipeline (landing → transform → map → CDP HTTP sink) can still be exercised end-to-end.

Related: [[AppsFlyer connector reduced to a single JSONL file-S3 sink]]

## Related

- [[AppsFlyer connector reduced to a single JSONL file-S3 sink]]
