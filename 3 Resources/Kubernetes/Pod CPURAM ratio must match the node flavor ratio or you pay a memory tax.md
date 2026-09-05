---
title: "Pod CPU:RAM ratio must match the node flavor ratio or you pay a memory tax"
created: 2026-09-03
type: lesson
status: seedling
source: "leo-customer360 dagster-scaling-analysis §13, 2026-09-03"
tags: [kubernetes, capacity-planning, cost, vks, gotcha]
---

# Pod CPU:RAM ratio must match the node flavor ratio or you pay a memory tax

Cloud "general" VM/node flavors come in a **fixed CPU:RAM ratio** (e.g. VNG/GreenNode `s2-general` is **1 vCPU : 2 GB**, billed linearly at one price per `(1 vCPU + 2 GB)` block). If a pod/container requests a **different ratio**, you must over-provision the scarcer dimension and the surplus of the other is **stranded but still billed**.

Rule of thumb on a 1:2 flavor: **blocks billed per pod = max(vCPU, RAM_GiB / 2)**.

Example (the gotcha): a `4 vCPU / 16 GiB` pod is 1:4. On a 1:2 flavor you must buy 8 vCPU to get 16 GiB, so it bills as **8 blocks, not 4** — a 2× "memory tax", half the CPU wasted. A `2 vCPU / 4 GiB` (1:2) or `4 vCPU / 8 GiB` (1:2) pod packs with zero waste.

Fixes, in order of preference:
1. Put memory-heavy workloads on a **memory-optimized node pool** whose ratio matches (1:4 / 1:8) — but confirm the flavor exists and its price (often a premium, and not always in the published rate card).
2. **Re-request** the workload closer to the flavor ratio if the real resource profile allows it.
3. Accept the tax (simplest, most expensive).

Corollary: bin-packing only lowers cost by reducing **total provisioned** vCPU+RAM; under linear per-block pricing, slicing the same total across more/fewer nodes does not change the bill — but a ratio mismatch inflates the total you are forced to provision.

## Related
[[Dagster worker pools are executor queues, not a pod kind]]

## Related

- [[Dagster worker pools are executor queues]]
- [[not a pod kind]]
