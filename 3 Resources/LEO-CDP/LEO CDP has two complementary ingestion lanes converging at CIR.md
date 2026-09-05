---
title: "LEO CDP has two complementary ingestion lanes converging at CIR"
created: 2026-08-25
type: concept
status: seedling
source: "LEOCDP web-tracking plan, session 2026-08-25"
tags: [leo-cdp, cdp, airbyte, web-tracking, identity-resolution, architecture]
---

# LEO CDP has two complementary ingestion lanes converging at CIR

The LEO CDP platform ingests customer data through **two complementary lanes** that converge at Customer Identity Resolution (CIR):

- **Airbyte lane (pull / batch ELT):** native + custom-CDK connectors pull from paid-media and 3rd-party platforms — Meta, Google/YouTube, TikTok, GA4, AppsFlyer, Zalo OA, PR/News — into a central Data Warehouse's raw schemas. Latency minutes–hours; data is 2nd/3rd-party (sampled, quota-limited).
- **Web Tracking lane (push / real-time):** the LEO Observer SDK sends first-party view/action/conversion/feedback events + profile updates from your own websites to an edge Collector, which lands them in a vStorage raw NDJSON lake and `cdp_raw_events` / `cdp_raw_profiles_stage`. Latency seconds; data is 1st-party, unsampled, full-fidelity, owned.

**They converge:** both feed one **Raw Data Layer** → **CIR** → **Unified Persona** → **Unified Campaign**, so a first-party conversion can be attributed to the originating ad (fbc/ttclid/gclid) on the same master profile.

**Unification trick:** the first-party vStorage raw NDJSON lake is itself a valid **Airbyte S3 source**, so a single Airbyte instance can load *both* lanes into the DW for BI — one tool, one warehouse.

The web lane fulfills the repo ROADMAP's short-term item 'Ingestion layer thật (Kafka/PubSub/RabbitMQ → cdp_raw_profiles_stage)'. See the plan at `deployments/docs/web-tracking-implementation-plan.md`.

Related: [[Lightweight-but-scalable web event collector pattern]], [[VNG Cloud vStorage is S3-compatible object storage]].

## Related

- [[Lightweight-but-scalable web event collector pattern]]
- [[VNG Cloud vStorage is S3-compatible object storage]]
