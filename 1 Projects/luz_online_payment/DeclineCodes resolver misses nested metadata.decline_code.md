---
ai_hash: 725a5467536236b3
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-31
entities:
- DeclineCodes resolver
- metadata.decline_code
- luz_online_payment
- Transaction.resolveDeclineCode()
- DeclineCodes.resolve()
- declineCode
- additionalProperties
- metadata
- Jackson
- Payrexx
- webhook path
- Transaction.metadata
- LinkedHashMap
- Metadata.java
- paypalBillingAgreementId
- LUZ-157476 commit
- synchronous path
- TransactionTask
- ConsumerServiceClientErrorException
- NotifiedTransactionService
- luz_store
- Content-Type
- MerchantService.java
- PayrexxNotifyTransactionResource
- APPLICATION_JSON
- form-urlencoded
- Payrexx delivers decline code only via webhook, not sync response
- Payrexx decline code lives at transaction.metadata.decline_code
source: LUZ-157476 code review 2026-07
status: seedling
tags:
- payrexx
- luz-157476
- decline-code
- luz-online-payment
- gotcha
title: DeclineCodes resolver misses nested metadata.decline_code
type: lesson
---

# DeclineCodes resolver misses nested metadata.decline_code

In `luz_online_payment`, `Transaction.resolveDeclineCode()` delegates to `DeclineCodes.resolve(declineCode, additionalProperties)`, which only inspects the top-level `declineCode` field plus top-level unknown keys captured by `@JsonAnySetter` into `additionalProperties`. Because `metadata` is a **known** field (`private Object metadata`), Jackson binds it directly and its nested contents never reach `additionalProperties`. Payrexx puts the code at `transaction.metadata.decline_code`, so **the current resolver misses it** even on the webhook path.

Fix: extend the resolver to also look inside the `metadata` map — `Transaction.metadata` is typed `Object` and deserializes to a `LinkedHashMap`, so read `((Map)metadata).get("decline_code")` (via the candidate-key list) with null/type guards (metadata can arrive as an empty array in other responses, which is why it is typed `Object`). `Metadata.java` models only `paypalBillingAgreementId`, so it is not usable for this.

Also: the LUZ-157476 commit wired `declineCode` onto the **synchronous** path (`TransactionTask` -> `ConsumerServiceClientErrorException`), which never carries the code. The plumbing must move to the webhook path (`NotifiedTransactionService` -> consumer) and forward to luz_store. And verify the webhook Content-Type: registered `type("json")` in `MerchantService.java:115` and `PayrexxNotifyTransactionResource` consumes `APPLICATION_JSON`, but the sample was form-urlencoded — a form-encoded POST would 415 the JSON endpoint.

Background: [[Payrexx delivers decline code only via webhook, not sync response]], [[Payrexx decline code lives at transaction.metadata.decline_code]].

## Related

- [[3 Resources/Work-Kepler/Payrexx/Payrexx delivers decline code only via webhook, not sync response]]
- [[Payrexx decline code lives at transaction.metadata.decline_code]]

%% ai-graph-start %%

**Related notes:**
- [[Payrexx notify webhook dispatches to two consumers, neither forwards decline code]]
- [[luz_online_payment silently drops Payrexx decline codes]]
- [[luz_online_payment notify webhook silently 400-rejects ~43% of Payrexx webhooks on dev]]
- [[Payrexx delivers decline code only via webhook, not sync response]]
- [[Payrexx card declines reach luz_store as ERROR with prose, not DECLINED]]

**Relations:**
- DeclineCodes resolver — *misses* — metadata.decline_code
- Transaction.resolveDeclineCode() — *delegates_to* — DeclineCodes.resolve()
- DeclineCodes.resolve() — *inspects* — declineCode
- DeclineCodes.resolve() — *inspects* — additionalProperties
- Jackson — *binds* — metadata
- metadata — *is_typed_as* — Object
- metadata — *deserializes_to* — LinkedHashMap
- Payrexx — *provides_code_at* — metadata.decline_code
- DeclineCodes resolver — *misses_on_path* — webhook path
- Metadata.java — *models* — paypalBillingAgreementId
- Metadata.java — *is_not_suitable_for* — declineCode
- LUZ-157476 commit — *wired* — declineCode
- LUZ-157476 commit — *wired_to* — synchronous path
- synchronous path — *does_not_carry* — declineCode
- plumbing — *should_move_to* — webhook path
- webhook path — *forwards_to* — luz_store
- MerchantService.java — *registers_type* — APPLICATION_JSON
- PayrexxNotifyTransactionResource — *consumes* — APPLICATION_JSON
- sample — *was* — form-urlencoded
- form-urlencoded — *is_incompatible_with* — APPLICATION_JSON
- Payrexx delivers decline code only via webhook, not sync response — *is_background_for* — DeclineCodes resolver
- Payrexx decline code lives at transaction.metadata.decline_code — *is_background_for* — DeclineCodes resolver
- luz_online_payment — *contains* — Transaction.resolveDeclineCode()
- luz_online_payment — *contains* — DeclineCodes.resolve()
- synchronous path — *involves* — TransactionTask
- synchronous path — *involves* — ConsumerServiceClientErrorException
- webhook path — *involves* — NotifiedTransactionService
- Payrexx — *delivers* — declineCode
- Payrexx — *delivers_via* — webhook path
- Payrexx — *does_not_deliver_via* — synchronous path

%% ai-graph-end %%