---
title: "ADK deploy targets compute matrix: Agent Engine vs Cloud Run vs GKE (CPU/GPU/TPU)"
created: 2026-09-03
type: concept
status: seedling
source: "research session 2026-09-03"
tags: [adk, gcp, agent-engine, cloud-run, gke, gpu, tpu]
---

# ADK deploy targets compute matrix: Agent Engine vs Cloud Run vs GKE (CPU/GPU/TPU)

ADK does not define its own runtime — it packages an agent that you deploy to one of three targets. Compute differs sharply:

| Target | Serverless | CPU | GPU | TPU |
|---|---|---|---|---|
| **Vertex AI Agent Engine** | fully managed | 1/2/4/6/8 vCPU, 1–32Gi mem (default 4vCPU/4Gi) | none | none |
| **Cloud Run** | scale-to-zero | yes | 1 per instance: NVIDIA L4 24GB (min 4CPU/16Gi) or RTX PRO 6000 Blackwell 96GB (min 20CPU/80Gi) | none |
| **GKE** (Autopilot/Standard) | managed, not serverless | yes | yes | yes: Cloud TPU v4/v5e/v5p/v6e via node pools |

**Scaling knobs (Agent Engine):** min_instances 0–10 (default 1), max_instances 1–1000 (default 100) but **capped at ≤100** when VPC-SC or PSC-I is enabled; container_concurrency default 9 (≈ 2·CPU+1).

**TPU takeaway:** neither serverless target offers TPU — GKE is the only path. For ADK agents TPU is effectively N/A because managed Gemini already runs on Google internal TPUs (abstracted). See [[ADK serverless agent tier is CPU-only; LLM compute is offloaded to a managed model API]].

## Related

- [[ADK serverless agent tier is CPU-only; LLM compute is offloaded to a managed model API]]
- [[Cloud Run static outbound IP needs VPC egress + Cloud NAT; Agent Engine private egress via PSC interface]]
