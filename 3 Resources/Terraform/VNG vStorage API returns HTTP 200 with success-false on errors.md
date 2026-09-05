---
title: "VNG vStorage API returns HTTP 200 with success-false on errors"
created: 2026-08-17
type: lesson
status: seedling
source: "session 2026-08-17"
tags: [vngcloud, vstorage, rest-api, gotcha, error-handling]
---

# VNG vStorage API returns HTTP 200 with success-false on errors

The VNG Cloud vStorage control-plane REST API (and other VNG APIs of the same family) returns **HTTP 200 even when the operation logically failed**. Success/failure is signalled in the JSON body, not the status line:

```json
{"success": false, "errorMsg": "Invalid input error (projectName must be provided; quotaInGBytes must be provided; projectType must be provided)", "code": 112, "data": null}
```

**Consequence for scripts/clients:** checking only the HTTP status (or using `curl -f`) is not enough — a create can "200 OK" while creating nothing. Always parse the body and branch on `success` (and surface `errorMsg`). A naive client that trusts the 200 will report a phantom success. On the flip side, `curl -f` (fail-on-4xx) never triggers here, so you also must not rely on it to catch these errors.

Discovered while writing deployments/storage/scripts/create-project.sh for leo-customer360: the create returned 200 with success:false because the body used the wrong field names.

Related: [[vStorage REST control-plane API: endpoints and vIAM bearer auth]].

## Related

- [[vStorage REST control-plane API: endpoints and vIAM bearer auth]]
