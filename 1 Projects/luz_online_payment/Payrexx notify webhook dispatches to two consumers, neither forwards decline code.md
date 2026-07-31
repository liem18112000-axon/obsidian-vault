---
title: "Payrexx notify webhook dispatches to two consumers, neither forwards decline code"
created: 2026-07-31
type: observation
status: seedling
source: "LUZ-157476 investigation 2026-07"
tags: [payrexx, webhook, luz-online-payment, luz-157476, gotcha]
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
