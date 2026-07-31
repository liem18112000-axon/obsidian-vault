---
title: "luz_online_payment silently drops Payrexx decline codes"
created: 2026-07-24
type: lesson
status: seedling
source: "session 2026-07-24, code investigation"
tags: [luz-online-payment, payrexx, jackson, gotcha, LUZ-157476]
---

# luz_online_payment silently drops Payrexx decline codes

luz_online_payment captures **no structured decline code** from Payrexx. The consumer response DTO `Transaction.java` declares no `code` / `errorCode` / `declineCode` / `reason` field, and the REST-client ObjectMapper is configured `FAIL_ON_UNKNOWN_PROPERTIES = false` (in `ObjectMapperFactory`).

Consequence: if Payrexx sends an ISO 8583 decline code on the wire, it is **silently dropped** during deserialization — no error, no log. The only decline detail that survives is the free-text `message` prose.

**Implication for the taxonomy work (LUZ-157476):** before a code->category mapping can be frozen, you must first confirm whether Payrexx even sends a code. The place to prove it is `Transaction.java` — add a candidate field, capture a declined sandbox payload, and observe. The lenient mapper is currently hiding the answer.

Repo: luz_online_payment.

## Related

- [[Payrexx card declines reach luz_store as ERROR with prose, not DECLINED]]
- [[1 Projects/luz_store/LUZ-157476/LUZ-157476 decline taxonomy maps codes at luz_online_payment boundary]]
