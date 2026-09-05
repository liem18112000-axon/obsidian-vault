---
title: "Deleting a prepaid cloud resource does not auto-refund; failed-provision charges need a manual support refund"
created: 2026-08-17
type: lesson
status: seedling
source: "session 2026-08-17"
tags: [cloud, billing, refund, vngcloud, vstorage, gotcha]
---

# Deleting a prepaid cloud resource does not auto-refund; failed-provision charges need a manual support refund

Two billing facts that matter when a cloud create charged money but the resource is wrong/absent:

1. **Deleting a prepaid resource does NOT auto-refund.** Prepaid/subscription resources (e.g. VNG vStorage-Gold billed as a 30-day block) release their quota on delete but the prepaid amount is generally forfeited — deletion is not a refund mechanism. Do not assume "delete = money back".

2. **Charges for a resource that never provisioned require a MANUAL support refund.** If the create failed (e.g. vStorage code 114) but the wallet was still charged, there is nothing to delete and no self-service refund — you must open a support ticket and have them restore the credit. Approval (full/partial) is at the provider's discretion; the case is strongest when you can show nothing was delivered (e.g. GET list is empty in every region).

3. **Refunds usually go back as wallet/credit, not cash.** If the balance was a credit/POC wallet, a granted refund restores wallet credit — not a card reversal — unless the original payment was a card charge.

Context: leo-customer360 vStorage — 6 failed creates charged ~3,162,000 VND with zero projects provisioned. Related: [[vStorage create-project code 114 is account-side, not a payload bug]], [[vStorage API project creation needs a billing order (payment method or POC wallet)]].

## Related

- [[vStorage create-project code 114 is account-side]]
- [[not a payload bug]]
