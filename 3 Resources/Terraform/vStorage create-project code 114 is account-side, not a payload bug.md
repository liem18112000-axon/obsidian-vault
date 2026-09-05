---
title: "vStorage create-project code 114 is account-side, not a payload bug"
created: 2026-08-17
type: observation
status: seedling
source: "session 2026-08-17 (live API probe)"
tags: [vngcloud, vstorage, billing, gotcha, object-storage]
---

# vStorage create-project code 114 is account-side, not a payload bug

> **CORRECTION (later same session):** the "account-side / can't place order"
> conclusion below was WRONG. Real cause = **wrong region**. And critically, the
> failed create **still CHARGED the wallet** — see the correction block.

**Real root cause:** `POST /api/v1/projects` was sent to `hcm03-api.vstorage.vngcloud.vn`, but **HCM03 has no object storage**. `GET /api/v1/regions` returns only **HCM04** and **HAN02** — object-storage regions are NOT the same as compute/vDB zones (postgres uses HCM03). Creating in an invalid region fails with **code 114** `"Could not send order request: Error occurred when creating project"`.

**Costly gotcha — it charges even though it "fails":** every code-114 response STILL placed a billing order and deducted the wallet. Six failed calls = 6 charges (~3,162,000 VND) with **zero** projects provisioned (`GET /projects` = `datas:null` in all regions). The billing order is placed BEFORE/independently of the region-validation that fails. Never retry a code-114 create in a loop — each attempt burns money. Fix: POST to a valid region host (hcm04/han02); refund the orphaned charges via support.

Original (payload-independence) observation, still true: the error is identical across Gold/Instant Archive, isPoc true/false, quota 30GB/1TB, autoRenew on/off — because none of those was the problem; the region (host) was.

Two other live facts learned:
- **Minimum Gold quota is 30 GB** (`quotaInGBytes` < 30 -> code 112 "Quota must from 30GB to 2000000GB", max 2,000,000 GB).
- **GET /api/v1/projects wraps the list in `datas`** (e.g. `{"success":true,"datas":null}` when you have zero projects) — NOT `data`/`projects`. A client that only reads `data` will never find existing projects.

Related: [[vStorage API project creation needs a billing order (payment method or POC wallet)]], [[VNG vStorage API returns HTTP 200 with success-false on errors]].

## Related

- [[vStorage API project creation needs a billing order (payment method or POC wallet)]]
- [[VNG vStorage API returns HTTP 200 with success-false on errors]]
