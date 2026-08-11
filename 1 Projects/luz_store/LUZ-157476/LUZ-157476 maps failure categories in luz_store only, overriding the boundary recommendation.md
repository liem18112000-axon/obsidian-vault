---
ai_hash: 60340aedb7e75e15
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-24
entities:
- LUZ-157476
- FailureCategory enum
- prose mapper
- luz_store
- luz_online_payment
- boundary recommendation
- OPEN-QUESTIONS Q1
- Payrexx
- TransactionStatus
- message
- i18n bundle keys
- DECLINED
- BLOCKED_BY_ISSUER
- TECHNICAL_ERROR
- OTHER
- NO_PAYMENT_METHOD
- local missing-card message
- Payrexx prose
- WARN log
- charge result
- controller flow
- email display point
- in-app display point
- LUZ-157476 decline taxonomy maps codes at luz_online_payment boundary
- Write-time localization into the existing message column avoids schema change
source: IMPLEMENTATION-PLAN refinement 2026-07-24
status: budding
tags:
- luz-store
- taxonomy
- design-decision
- scope
title: LUZ-157476 maps failure categories in luz_store only, overriding the boundary
  recommendation
type: argument
---

# LUZ-157476 maps failure categories in luz_store only, overriding the boundary recommendation

Final scope decision for LUZ-157476: the FailureCategory enum and the prose mapper live entirely in luz_store — luz_online_payment is not changed. This consciously overrides the earlier 'map at the luz_online_payment boundary' recommendation (OPEN-QUESTIONS Q1). Justification: no structured code exists on the wire anyway (code-verified), so the upstream service adds nothing today except being one hop closer to Payrexx; a single-repo change ships without deploy-order coordination, wire DTO changes, or schema migration; and the mapper is one static class keyed to (TransactionStatus, message), movable upstream later without touching the i18n bundle keys.

Mapper rules consolidated in luz_store: DECLINED→BLOCKED_BY_ISSUER (message is null), TECHNICAL_ERROR→OTHER, local missing-card message→NO_PAYMENT_METHOD, else prefix-strip + contains on Payrexx prose, fallback OTHER + WARN log. Map once per charge result in the controller flow (:869-887), feed both display points (email :893, in-app :628 write-time-localized).

## Related
- [[LUZ-157476 decline taxonomy maps codes at luz_online_payment boundary]]
- [[Write-time localization into the existing message column avoids schema change]]

%% ai-graph-start %%

**Related notes:**
- [[LUZ-157476 decline taxonomy maps codes at luz_online_payment boundary]]
- [[LUZ-157476 proposes seven failure categories with per-channel customer copy]]
- [[Persist payment failure category at charge time, never derive from prose]]
- [[LUZ-157476 decline-code flow luz-online-payment forwards, luz_store maps]]
- [[luz_online_payment silently drops Payrexx decline codes]]

**Relations:**
- LUZ-157476 — *maps* — FailureCategory enum
- LUZ-157476 — *maps* — prose mapper
- FailureCategory enum — *lives in* — luz_store
- prose mapper — *lives in* — luz_store
- luz_online_payment — *is not changed by* — LUZ-157476
- LUZ-157476 — *overrides* — boundary recommendation
- boundary recommendation — *is from* — OPEN-QUESTIONS Q1
- luz_online_payment — *is one hop closer to* — Payrexx
- prose mapper — *is keyed to* — TransactionStatus
- prose mapper — *is keyed to* — message
- prose mapper — *is movable upstream later without touching* — i18n bundle keys
- prose mapper — *has rules consolidated in* — luz_store
- DECLINED — *maps to* — BLOCKED_BY_ISSUER
- TECHNICAL_ERROR — *maps to* — OTHER
- local missing-card message — *maps to* — NO_PAYMENT_METHOD
- Payrexx prose — *falls back to* — OTHER
- Payrexx prose — *triggers* — WARN log
- prose mapper — *maps once per* — charge result
- controller flow — *feeds* — email display point
- controller flow — *feeds* — in-app display point
- LUZ-157476 — *is related to* — LUZ-157476 decline taxonomy maps codes at luz_online_payment boundary
- LUZ-157476 — *is related to* — Write-time localization into the existing message column avoids schema change

%% ai-graph-end %%