---
title: "vMonitor create-alarm field values reverse-engineered (avg, gte, CRITICAL, resendPeriod minutes)"
created: 2026-08-17
type: reference
status: seedling
source: "session 2026-08-17"
tags: [greennode, vngcloud, vmonitor, alarm, api]
---

# vMonitor create-alarm field values reverse-engineered (avg, gte, CRITICAL, resendPeriod minutes)

vMonitor `POST /vmonitor-api/api/v1/alarms/metrics` validates one field at a time (400 with the offending field). Values reverse-engineered by live-probing against a real vDB instance:

- **description**: plain text only — rejects special chars like `>=` ('... for the field description is an invalid').
- **metricStatistic**: `avg` (NOT `AVERAGE`). Lowercase.
- **condition**: `gte` (NOT `>=`) — the comparison-operator enum (likely also gt/lte/lt/eq).
- **severity**: `CRITICAL` is valid; `MAJOR` and `WARNING` are INVALID (the warning-level value is still unknown).
- **resendPeriod**: integer **minutes**, range **10-1440**.
- Required fields seen: name, description, metricProduct, metricName, metricStatistic, metricPeriod, metricFilter, condition, thresholdMethod, thresholdValue, severity, interval, checkTime, resendEnabled/Period/Times.

**Still unknown → cause an opaque HTTP 500** (not a 400): the exact vDB **metricName** strings, the **metricProduct** code, and the **metricFilter** shape. A 500 (vs 400) means those passed field-presence validation but the backend couldn't resolve the metric. Get them in one shot via a browser **DevTools -> Network 'Copy as cURL'** of a manually-created console alarm. Implemented in leo-customer360 deployments/postgres/vmonitor/setup_alarms.py. See [[vMonitor is not in the vngcloud Terraform provider; alarms go via POST vmonitor-api/api/v1/alarms/metrics]].

## Related

- [[vMonitor is not in the vngcloud Terraform provider; alarms go via POST vmonitor-api/api/v1/alarms/metrics]]
