---
title: "vMonitor is not in the vngcloud Terraform provider; alarms go via POST vmonitor-api/api/v1/alarms/metrics"
created: 2026-08-17
type: reference
status: seedling
source: "session 2026-08-17"
tags: [greennode, vngcloud, vmonitor, monitoring, api, terraform]
---

# vMonitor is not in the vngcloud Terraform provider; alarms go via POST vmonitor-api/api/v1/alarms/metrics

GreenNode/VNG Cloud **vMonitor** (monitoring + alerting) is **NOT part of the vngcloud Terraform provider** — verified by listing every resource type in the provider binary (`grep -aoE 'vngcloud_[a-z0-9_]+' terraform-provider-vngcloud*.exe`): only vdb_/vserver_/vlb_/vks_ resources, no monitor/alarm/alert. So alarms/dashboards can't be Terraform-managed; use the vMonitor console or API.

**Create a metric alarm:** `POST https://vmonitorapis.vngcloud.vn/vmonitor-api/api/v1/alarms/metrics` (header `Authorization: Bearer <token>`). Body (verified schema): `name, description, metricProduct, metricName, metricStatistic, metricPeriod, metricFilter{}, thresholdMethod, thresholdValue, severity, interval, checkTime, resendEnabled/Period/Times`. metricFilter targets the resource (e.g. the vDB instance id).

**Undocumented (verify via console DevTools capture):** the exact `metricName` strings per product (vDB CPU/mem/disk/conn), the `metricProduct` code, the `metricFilter` key shape, and how a notification **receiver** id attaches. vMonitor auto-collects vDB metrics; 'setup' = alarms + a receiver. Implemented in leo-customer360 deployments/postgres/vmonitor/ (runbook + setup_alarms.py). See [[Recover undocumented vDB API endpoints by grep-ing the vngcloud provider binary]].

## Related

- [[Recover undocumented vDB API endpoints by grep-ing the vngcloud provider binary]]
