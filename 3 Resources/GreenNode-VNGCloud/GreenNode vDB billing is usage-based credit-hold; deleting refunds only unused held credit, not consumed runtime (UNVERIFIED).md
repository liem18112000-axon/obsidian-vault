---
title: "GreenNode vDB billing is usage-based credit-hold; deleting refunds only unused held credit, not consumed runtime (UNVERIFIED)"
created: 2026-08-17
type: observation
status: seedling
source: "session 2026-08-17"
tags: [greennode, vngcloud, vdb, billing, unverified]
---

# GreenNode vDB billing is usage-based credit-hold; deleting refunds only unused held credit, not consumed runtime (UNVERIFIED)

**CORRECTION (2026-08-17, from a real billing receipt):** vDB can be sold as a **FIXED-TERM PREPAID** resource, NOT hourly. A real purchase line read: `[CREATE] vserver - dbaas. Item: db.s-general-8x16,SSD-IOPS3200.dbaas. Time period: 30d 0h 0m  -8,160,000 VND`. For a prepaid 30-day instance, **early deletion likely forfeits the unused term** (prepaid/committed cloud resources are commonly non-refundable or only partially prorated) — the hourly credit-hold framing below did NOT apply to this instance. Refund-on-early-delete for prepaid vDB is provider-policy-specific and **UNKNOWN — check the billing dashboard / ask GreenNode support.** **Lesson: check whether a vDB is hourly PAYG vs fixed-term prepaid BEFORE deleting.**

---

**UNVERIFIED / single-source** (VNG Cloud/GreenNode billing docs, paraphrased) — confirm in the billing console or with support before relying on it.

GreenNode/VNG Cloud vDB appears to bill **usage-based** on a **daily credit-hold model**: each day the system temporarily HOLDS credit based on actual usage, then **refunds any over-held amount** back to the account. Supports prepaid + postpaid.

Implication for 'do I get credit back if I delete the database?':
- **Consumed runtime is billed** — deleting does NOT refund the hours the instance already ran.
- Deleting **stops future charges/holds**; credit held-but-not-consumed is reconciled back to the **account balance/credit** (not necessarily the card).
- **Committed/reserved prepaid terms** (if any) have separate early-deletion refund rules — unknown, ask support.
- Gotchas: auto-backups are deleted with the instance; **manual snapshots persist and keep costing** storage; a stopped/shutdown instance usually still bills for storage.

Context: asked while running customer360-pg-uat (8vCPU/16GB, 250GB) in leo-customer360. To stop spend via repo: `./deploy.sh uat destroy`. See [[Provision GreenNode/VNG Cloud vDB PostgreSQL with the vngcloud Terraform provider]].

## Related

- [[Provision GreenNode/VNG Cloud vDB PostgreSQL with the vngcloud Terraform provider]]
