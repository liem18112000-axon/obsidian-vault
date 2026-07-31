---
title: "DeclineCodes resolver misses nested metadata.decline_code"
created: 2026-07-31
type: lesson
status: seedling
source: "LUZ-157476 code review 2026-07"
tags: [payrexx, luz-157476, decline-code, luz-online-payment, gotcha]
---

# DeclineCodes resolver misses nested metadata.decline_code

In `luz_online_payment`, `Transaction.resolveDeclineCode()` delegates to `DeclineCodes.resolve(declineCode, additionalProperties)`, which only inspects the top-level `declineCode` field plus top-level unknown keys captured by `@JsonAnySetter` into `additionalProperties`. Because `metadata` is a **known** field (`private Object metadata`), Jackson binds it directly and its nested contents never reach `additionalProperties`. Payrexx puts the code at `transaction.metadata.decline_code`, so **the current resolver misses it** even on the webhook path.

Fix: extend the resolver to also look inside the `metadata` map — `Transaction.metadata` is typed `Object` and deserializes to a `LinkedHashMap`, so read `((Map)metadata).get("decline_code")` (via the candidate-key list) with null/type guards (metadata can arrive as an empty array in other responses, which is why it is typed `Object`). `Metadata.java` models only `paypalBillingAgreementId`, so it is not usable for this.

Also: the LUZ-157476 commit wired `declineCode` onto the **synchronous** path (`TransactionTask` -> `ConsumerServiceClientErrorException`), which never carries the code. The plumbing must move to the webhook path (`NotifiedTransactionService` -> consumer) and forward to luz_store. And verify the webhook Content-Type: registered `type("json")` in `MerchantService.java:115` and `PayrexxNotifyTransactionResource` consumes `APPLICATION_JSON`, but the sample was form-urlencoded — a form-encoded POST would 415 the JSON endpoint.

Background: [[Payrexx delivers decline code only via webhook, not sync response]], [[Payrexx decline code lives at transaction.metadata.decline_code]].

## Related

- [[3 Resources/Work-Kepler/Payrexx/Payrexx delivers decline code only via webhook, not sync response]]
- [[Payrexx decline code lives at transaction.metadata.decline_code]]
