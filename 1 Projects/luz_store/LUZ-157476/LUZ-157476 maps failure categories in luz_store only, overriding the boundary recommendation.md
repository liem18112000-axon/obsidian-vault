---
title: "LUZ-157476 maps failure categories in luz_store only, overriding the boundary recommendation"
created: 2026-07-24
type: argument
status: budding
source: "IMPLEMENTATION-PLAN refinement 2026-07-24"
tags: [luz-store, taxonomy, design-decision, scope]
---

# LUZ-157476 maps failure categories in luz_store only, overriding the boundary recommendation

Final scope decision for LUZ-157476: the FailureCategory enum and the prose mapper live entirely in luz_store — luz_online_payment is not changed. This consciously overrides the earlier 'map at the luz_online_payment boundary' recommendation (OPEN-QUESTIONS Q1). Justification: no structured code exists on the wire anyway (code-verified), so the upstream service adds nothing today except being one hop closer to Payrexx; a single-repo change ships without deploy-order coordination, wire DTO changes, or schema migration; and the mapper is one static class keyed to (TransactionStatus, message), movable upstream later without touching the i18n bundle keys.

Mapper rules consolidated in luz_store: DECLINED→BLOCKED_BY_ISSUER (message is null), TECHNICAL_ERROR→OTHER, local missing-card message→NO_PAYMENT_METHOD, else prefix-strip + contains on Payrexx prose, fallback OTHER + WARN log. Map once per charge result in the controller flow (:869-887), feed both display points (email :893, in-app :628 write-time-localized).

## Related
- [[LUZ-157476 decline taxonomy maps codes at luz_online_payment boundary]]
- [[Write-time localization into the existing message column avoids schema change]]
