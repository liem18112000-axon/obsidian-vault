---
title: "Invoice Run v2 silently skips an individual whose store customer has a blank partner_uri"
created: 2026-08-04
type: lesson
status: seedling
source: "session 2026-08-04 dev investigation"
tags: [luz_store, invoice-run-v2, partner_uri, individual, gotcha, seeding]
---

# Invoice Run v2 silently skips an individual whose store customer has a blank partner_uri

In **Invoice Run v2** (luz_store), a `billing` row can pass the run-selection **stamp** (get `invoice_run_uuid` set) yet never become an `invoice_item` — because item-building drops any store customer whose **`partner_uri` is blank**.

## The filter
`InvoiceRunServiceV2.getStoreCustomerByCustomerIds` (`service/InvoiceRunServiceV2.java:653`) builds the customer map with:
```java
storeCustomers.stream().filter(item -> StringUtils.isNotBlank(item.get_partnerUri()))
```
So a `customer` with empty `partner_uri` is excluded. Then `buildBasicInfoInvoiceItems` (L668-671) finds `mapStoreCustomer.get(id) == null` and logs:
```
WARNING InvoiceRunServiceV2  Skip invoicing of store customer id <ID> due to missing customer information
```
and produces no invoice item. `partner_uri` is also needed downstream at `getTotalAmount`/`getCustomerCache` (L694), so it must point to a REAL luzfin_finance customer, not just be non-blank.

## Why this is easy to miss
- The billing **selection/stamp** (`BillingDao.updateBillingsWithUuidForInvoicing`) does NOT check the customer at all — it filters only on price_plan / no_invoiced / invoice_number / tenant_type. So the row looks "collected" (has invoice_run_uuid) but isn`t invoiced.
- The skip is logged by **store customer id**, NOT by company_uri — so grepping dev logs for the profile/company URI in the calc window finds nothing. Search by the numeric `customer.id`, or for the literal string `Skip invoicing of store customer`.
- `partner_uri` is the link to the customer`s luzfin_finance record (`/luzfin_finance/api/<financeTenant>/companies/1/customers/<customerId>`). Freshly-seeded/incomplete individual customers often lack it; properly-onboarded ones have it.

## Verified on dev (2026-08-04)
Individual `9388f0ab` (store customer id 392709, blank partner_uri) was skipped by run 2778 (`d675a04b`), which stamped 955 billing rows but produced only 1 invoice item — for a different individual (`3fce29c4`, customer 384940, which HAS a partner_uri). Log line seen at the exact prepareInvoiceData timestamp.

Relates to seeding failing individual charges — a seeded billing row is useless if its customer has no partner_uri. See [[Failed INDIVIDUAL Invoice Run v2 charge is tracked by three distinct state fields]].

## Related

- [[Failed INDIVIDUAL Invoice Run v2 charge is tracked by three distinct state fields]]
