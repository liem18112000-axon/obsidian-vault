---
title: "vStorage API project creation needs a billing order (payment method or POC wallet)"
created: 2026-08-17
type: lesson
status: seedling
source: "session 2026-08-17"
tags: [vngcloud, vstorage, billing, order, gotcha, object-storage]
---

# vStorage API project creation needs a billing order (payment method or POC wallet)

Creating a vStorage project via `POST /api/v1/projects` is not just a metadata write — the backend forwards it to VNG's **billing/order service**. Both paid and POC projects go through a **Checkout / order** step: a paid project needs a payment method (credit card / Momo / Zalopay), a POC project spends **POC-wallet credits** (POC is not free — it draws down granted credit).

If the account can't place the order, the API returns **HTTP 200** with `{"success": false, "errorMsg": "Could not send order request: Error occurred when creating project"}` and **nothing is created**. This is a billing/account condition, not a payload bug — it appears AFTER the field-validation errors are resolved (correct fields: projectName, projectType, quotaInGBytes).

**Practical takeaway:** the reliable way to create the project is the **console checkout** (`vstorage.console.vngcloud.vn` -> Create a Project -> Checkout). The API create only succeeds where the account/service-account can place the order programmatically (funded wallet or pre-authorized payment, and the IAM service account has billing/order permission). An idempotent API helper is still useful as a **lookup** — after console creation it lists projects and returns the project_id.

Reinforces the design choice: keep the billed project OUT of Terraform; bootstrap/lookup separately. Related: [[vStorage project is a paid prerequisite Terraform cannot create]], [[VNG vStorage API returns HTTP 200 with success-false on errors]], [[Keep billed bootstrap resources out of Terraform state]].

## Related

- [[vStorage project is a paid prerequisite Terraform cannot create]]
- [[VNG vStorage API returns HTTP 200 with success-false on errors]]
