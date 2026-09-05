---
title: "VNG Cloud publishes no static price tables — calculator or quote only"
created: 2026-08-26
type: lesson
status: seedling
source: "session 2026-08-26 VKS research"
tags: [vng-cloud, pricing, gotcha, greennode]
---

# VNG Cloud publishes no static price tables — calculator or quote only

> **CORRECTION (2026-08-26, second pass):** the GitBook *docs* carry no numbers and the **calculator is decommissioned** (`calculator.console.greennode.ai` returns NXDOMAIN), BUT the real, current **PAYG list prices are published** — embedded as server-rendered CMS data in the VNG **product pages** `vngcloud.vn/vi/product/{vserver,vdb,vstorage}`. So VNG pricing *is* obtainable without a quote; you just have to read the product pages, not the docs or the (dead) calculator.

Published monthly VND list prices (VAT-incl, PAYG):
- vServer is **linear per (1 vCPU + 2 GB) block**: `s-general` = **236,500**/block, `s2-general` = **283,800**/block (so s2-general-2x4 = 567,600; 4x8 = 1,135,200; 8x16 = 2,270,400).
- Managed PostgreSQL / MemStore (names changed to `db.v1.*`/`db.v2.*`, `.b100` = 100 GB incl.): 2x4 = **840,000**, 8x16 = **3,360,000**; MemStore reuses RDS instance prices.
- VKS control plane = **free**. Extra DB storage ≈ 2,000 VND/GB/mo.

Still genuinely **not published anywhere** (calculator-only → estimate + confirm in console): **block-volume / PVC SSD per-GB** and **load-balancer package (`NLB_Small`) + egress VND/GB**. VNG quotes only in VND (no FX on any page); use ≈ 26,000 VND/USD.

Practical takeaway: for a VNG cost model, scrape the three product pages for compute/DB, and treat only PVC + NLB as fill-in estimates (they usually cancel in a like-for-like delta).

Related: [[VNG Cloud VKS control plane is free; worker nodes billed as vServer VMs]]

## Related

- [[VNG Cloud VKS control plane is free; worker nodes billed as vServer VMs]]
