---
ai_hash: 5e7331f75aaf6954
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-24
entities:
- luz_online_payment
- Payrexx
- Transaction.java
- ObjectMapperFactory
- ObjectMapper
- Payrexx decline codes
- ISO 8583 decline code
- structured decline code
- decline code field
- message (prose)
- LUZ-157476 (taxonomy work)
- luz_store
- ERROR (status)
- DECLINED (status)
- FAIL_ON_UNKNOWN_PROPERTIES = false (setting)
- deserialization
- decline (event)
- luz_online_payment boundary
source: session 2026-07-24, code investigation
status: seedling
tags:
- luz-online-payment
- payrexx
- jackson
- gotcha
- LUZ-157476
title: luz_online_payment silently drops Payrexx decline codes
type: lesson
---

# luz_online_payment silently drops Payrexx decline codes

luz_online_payment captures **no structured decline code** from Payrexx. The consumer response DTO `Transaction.java` declares no `code` / `errorCode` / `declineCode` / `reason` field, and the REST-client ObjectMapper is configured `FAIL_ON_UNKNOWN_PROPERTIES = false` (in `ObjectMapperFactory`).

Consequence: if Payrexx sends an ISO 8583 decline code on the wire, it is **silently dropped** during deserialization — no error, no log. The only decline detail that survives is the free-text `message` prose.

**Implication for the taxonomy work (LUZ-157476):** before a code->category mapping can be frozen, you must first confirm whether Payrexx even sends a code. The place to prove it is `Transaction.java` — add a candidate field, capture a declined sandbox payload, and observe. The lenient mapper is currently hiding the answer.

Repo: luz_online_payment.

## Related

- [[Payrexx card declines reach luz_store as ERROR with prose, not DECLINED]]
- [[1 Projects/luz_store/LUZ-157476/LUZ-157476 decline taxonomy maps codes at luz_online_payment boundary]]

%% ai-graph-start %%

**Related notes:**
- [[LUZ-157476 decline taxonomy maps codes at luz_online_payment boundary]]
- [[Payrexx card declines reach luz_store as ERROR with prose, not DECLINED]]
- [[KlaraPay DTOs are code-blind - lenient Jackson drops any Payrexx decline code]]
- [[Payrexx v1.0 charge API returns only status+message on failure — no ISO 8583 code]]
- [[LUZ-157476 decline-code flow luz-online-payment forwards, luz_store maps]]

**Relations:**
- luz_online_payment — *silently drops* — Payrexx decline codes
- luz_online_payment — *captures no* — structured decline code
- structured decline code — *originates from* — Payrexx
- Transaction.java — *declares no* — decline code field
- ObjectMapperFactory — *configures* — ObjectMapper
- ObjectMapper — *is configured with* — FAIL_ON_UNKNOWN_PROPERTIES = false (setting)
- ISO 8583 decline code — *is silently dropped during* — deserialization
- deserialization — *is affected by* — FAIL_ON_UNKNOWN_PROPERTIES = false (setting)
- message (prose) — *is only surviving detail of* — decline (event)
- LUZ-157476 (taxonomy work) — *requires confirmation if* — Payrexx
- Payrexx — *sends* — decline code field
- Transaction.java — *is place to prove* — Payrexx
- Payrexx — *sends* — decline code field
- Payrexx card declines — *reach* — luz_store
- Payrexx card declines — *reach as* — ERROR (status)
- Payrexx card declines — *do not reach as* — DECLINED (status)
- LUZ-157476 (taxonomy work) — *maps codes at* — luz_online_payment boundary

%% ai-graph-end %%