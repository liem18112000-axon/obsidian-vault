---
ai_hash: 37d41843d513deaa
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-31
entities:
- Payrexx
- webhook
- decline code
- luz_online_payment
- NotifiedTransactionService
- NotifiedTransactionService.handle()
- OrderGateway status
- NotifiedTransactionConsumerFactory
- orderGateway.getGatewayType()
- ONLINE_SHOP
- OnlineShopTransactionConsumer
- onlineShopAdapterCaller
- onlineShopAdapterCaller.notifyTransaction()
- DTO
- KlaraPayTransaction.toKlaraPayTx()
- WIDGET_STORE
- ServiceTenantTransactionPaymentConsumer
- serviceTenantPaymentCaller
- CustomerPaymentTransaction
- transaction.metadata
- transaction.metadata.decline_code
- luz_store
- LUZ-157476
- declineCode field
- resolver
- Retry asymmetry
- exceptions
- Payrexx retry
- downstream failure
- ValidationException
- gateway not found
- status update
- Declined payloads
- bean validation
- Transaction.payment
- invoice
- '@NotNull @Valid'
- declined charge
- endpoint
- '@PermitAll'
- Payrexx signature verification
- Factory
- service tenant transaction consumer
- log bug
- luz_online_payment Payrexx webhook uses JSON-only Jackson mapper with FAIL_ON_UNKNOWN_PROPERTIES
  off
- DeclineCodes resolver misses nested metadata.decline_code
source: LUZ-157476 investigation 2026-07
status: seedling
tags:
- payrexx
- webhook
- luz-online-payment
- luz-157476
- gotcha
title: Payrexx notify webhook dispatches to two consumers, neither forwards decline
  code
type: observation
---

# Payrexx notify webhook dispatches to two consumers, neither forwards decline code

In `luz_online_payment`, `NotifiedTransactionService.handle()` updates the OrderGateway status then dispatches to one of two consumers via `NotifiedTransactionConsumerFactory`, keyed on `orderGateway.getGatewayType()`:

- `ONLINE_SHOP` -> `OnlineShopTransactionConsumer` -> `onlineShopAdapterCaller.notifyTransaction()`, DTO built by `KlaraPayTransaction.toKlaraPayTx()`.
- `WIDGET_STORE` -> `ServiceTenantTransactionPaymentConsumer` -> `serviceTenantPaymentCaller`, DTO `CustomerPaymentTransaction`.

Neither DTO carries a decline code and neither reads `transaction.metadata`, so the Payrexx decline code (at `transaction.metadata.decline_code`) is dropped before luz_store on BOTH paths. Wiring LUZ-157476 through the webhook means adding a `declineCode` field to whichever DTO the target gateway type uses AND making the resolver read inside metadata.

Two behavioral gotchas:
1. Retry asymmetry: `ServiceTenantTransactionPaymentConsumer` catches+logs its own exceptions (returns 200, no Payrexx retry), but `OnlineShopTransactionConsumer` does not (a downstream failure -> non-2xx -> Payrexx retries). A "gateway not found" throws ValidationException -> 500 -> retry; the whole handle is `@Transactional` so the status update rolls back too.
2. Declined payloads may fail bean validation: `Transaction.payment` and `invoice` are `@NotNull @Valid`. If Payrexx omits `payment` on a declined charge, the webhook is rejected (400) and never processed — directly relevant to LUZ-157476 (which is about declined txns). Verify against a real declined webhook.

Also: the endpoint is `@PermitAll` with no Payrexx signature verification (spoofable). Factory logs "service tenant transaction consumer" for BOTH branches (copy-paste log bug).

Related: [[luz_online_payment Payrexx webhook uses JSON-only Jackson mapper with FAIL_ON_UNKNOWN_PROPERTIES off]], [[DeclineCodes resolver misses nested metadata.decline_code]].

## Related

- [[luz_online_payment Payrexx webhook uses JSON-only Jackson mapper with FAIL_ON_UNKNOWN_PROPERTIES off]]
- [[DeclineCodes resolver misses nested metadata.decline_code]]

%% ai-graph-start %%

**Related notes:**
- [[luz_online_payment notify webhook silently 400-rejects ~43% of Payrexx webhooks on dev]]
- [[DeclineCodes resolver misses nested metadata.decline_code]]
- [[Payrexx card declines reach luz_store as ERROR with prose, not DECLINED]]
- [[KlaraPay DTOs are code-blind - lenient Jackson drops any Payrexx decline code]]
- [[luz_online_payment silently drops Payrexx decline codes]]

**Relations:**
- Payrexx — *dispatches* — webhook
- webhook — *dispatches to* — OnlineShopTransactionConsumer
- webhook — *dispatches to* — ServiceTenantTransactionPaymentConsumer
- webhook — *does not forward* — decline code
- luz_online_payment — *contains* — NotifiedTransactionService
- NotifiedTransactionService.handle() — *updates* — OrderGateway status
- NotifiedTransactionService.handle() — *dispatches to* — NotifiedTransactionConsumerFactory
- NotifiedTransactionConsumerFactory — *keyed on* — orderGateway.getGatewayType()
- orderGateway.getGatewayType() — *is* — ONLINE_SHOP
- ONLINE_SHOP — *routes to* — OnlineShopTransactionConsumer
- OnlineShopTransactionConsumer — *calls* — onlineShopAdapterCaller.notifyTransaction()
- onlineShopAdapterCaller.notifyTransaction() — *uses* — DTO
- DTO — *built by* — KlaraPayTransaction.toKlaraPayTx()
- orderGateway.getGatewayType() — *is* — WIDGET_STORE
- WIDGET_STORE — *routes to* — ServiceTenantTransactionPaymentConsumer
- ServiceTenantTransactionPaymentConsumer — *calls* — serviceTenantPaymentCaller
- serviceTenantPaymentCaller — *uses* — CustomerPaymentTransaction
- CustomerPaymentTransaction — *is a* — DTO
- DTO — *does not carry* — decline code
- DTO — *does not read* — transaction.metadata
- transaction.metadata — *contains* — transaction.metadata.decline_code
- transaction.metadata.decline_code — *is a* — decline code
- decline code — *is dropped before* — luz_store
- LUZ-157476 — *is about* — declined txns
- LUZ-157476 — *requires adding* — declineCode field
- declineCode field — *to* — DTO
- LUZ-157476 — *requires* — resolver
- resolver — *to read* — transaction.metadata
- ServiceTenantTransactionPaymentConsumer — *catches* — exceptions
- ServiceTenantTransactionPaymentConsumer — *prevents* — Payrexx retry
- OnlineShopTransactionConsumer — *does not catch* — exceptions
- OnlineShopTransactionConsumer — *causes* — Payrexx retry
- Payrexx retry — *on* — downstream failure
- gateway not found — *throws* — ValidationException
- ValidationException — *causes* — Payrexx retry
- status update — *rolls back with* — ValidationException
- Declined payloads — *may fail* — bean validation
- Transaction.payment — *is* — @NotNull @Valid
- invoice — *is* — @NotNull @Valid
- Payrexx — *omits* — Transaction.payment
- Transaction.payment — *on* — declined charge
- webhook — *is rejected if* — Transaction.payment
- endpoint — *is* — @PermitAll
- endpoint — *lacks* — Payrexx signature verification
- Factory — *has* — log bug
- log bug — *involves* — service tenant transaction consumer
- luz_online_payment Payrexx webhook uses JSON-only Jackson mapper with FAIL_ON_UNKNOWN_PROPERTIES off — *relates to* — webhook
- DeclineCodes resolver misses nested metadata.decline_code — *relates to* — resolver
- DeclineCodes resolver misses nested metadata.decline_code — *relates to* — decline code

%% ai-graph-end %%