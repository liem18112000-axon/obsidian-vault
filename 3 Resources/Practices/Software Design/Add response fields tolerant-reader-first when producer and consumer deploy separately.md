---
title: "Add response fields tolerant-reader-first when producer and consumer deploy separately"
created: 2026-07-24
type: lesson
status: seedling
source: "LUZ-157476 implementation plan 2026-07-24"
tags: [rest, jackson, rollout, api-evolution, design-decision]
---

# Add response fields tolerant-reader-first when producer and consumer deploy separately

When a producer service adds a field to a response DTO consumed by a separately-deployed service, ship the CONSUMER change first (tolerant reader), then the producer. If the consumer's Jackson config is strict (FAIL_ON_UNKNOWN_PROPERTIES=true or unknown), a producer-first deploy breaks every call the moment the new field appears.

Applied in LUZ-157476 rollout order: 1) luz_store gets the failureCategory field on its KlaraTransactionRequest DTO (and its ObjectMapperContextResolver leniency is verified), 2) only then luz_online_payment starts sending it, 3) display work last. Each step stays backward compatible with the previous one.

## Related
- [[LUZ-157476 decline taxonomy maps codes at luz_online_payment boundary]]
- [[KlaraPay DTOs are code-blind - lenient Jackson drops any Payrexx decline code]]
