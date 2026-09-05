---
title: "VNG Cloud VKS control plane is free; worker nodes billed as vServer VMs"
created: 2026-08-26
type: concept
status: seedling
source: "session 2026-08-26 leo-customer360 VKS research"
tags: [vng-cloud, vks, kubernetes, pricing, greennode]
---

# VNG Cloud VKS control plane is free; worker nodes billed as vServer VMs

VKS (VNG Cloud Kubernetes Service, rebranded GreenNode Kubernetes Service) runs a **fully-managed, HA control plane at no charge** — you pay only for the resources the cluster consumes. **Worker nodes are billed as ordinary vServer VMs** at the same flavor prices as standalone vServers, plus block-storage PVCs and any load balancers.

Cost consequence: migrating VMs → VKS carries **no Kubernetes control-plane premium** (unlike GKE/EKS ≈ $73/cluster/mo). The cost delta is decided purely by whether the node pool provisions more or less vCPU/RAM than the VMs it replaces — bin-packing dedicated-per-service boxes into a shared pool tends to be cost-neutral-to-cheaper.

Other billing gotcha: every Kubernetes **Service type=LoadBalancer provisions a separate billed VNG NLB** (default package 'NLB Small') — consolidate behind one ingress LB and reuse it via the 'vks.vngcloud.vn/load-balancer-id' annotation.

Source: docs.greennode.ai/vks/cach-tinh-gia (control-plane-free), /vks/node-groups (nodes=VMs), /vks/network/loadbalancer.

Related: [[VNG Cloud publishes no static price tables — calculator or quote only]] · [[VNG Cloud docs rebranded to GreenNode (docs.vngcloud.vn redirects to docs.greennode.ai)]]

## Related

- [[VNG Cloud publishes no static price tables — calculator or quote only]]
- [[VNG Cloud docs rebranded to GreenNode (docs.vngcloud.vn redirects to docs.greennode.ai)]]
