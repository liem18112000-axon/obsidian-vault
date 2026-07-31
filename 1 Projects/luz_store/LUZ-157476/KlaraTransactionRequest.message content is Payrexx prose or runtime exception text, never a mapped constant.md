---
title: "KlaraTransactionRequest.message content is Payrexx prose or runtime exception text, never a mapped constant"
created: 2026-07-24
type: lesson
status: seedling
source: "session 2026-07-24, code investigation"
tags: [luz-online-payment, payrexx, gotcha, LUZ-157476]
---

# KlaraTransactionRequest.message content is Payrexx prose or runtime exception text, never a mapped constant

The `message` field on `KlaraTransactionRequest` (luz_online_payment) is never assigned a string literal on the normal charge/refund result path — every `setMessage(...)` passes dynamic exception text. Its content is one of exactly three kinds:

1. **Payrexx-authored prose** (common decline case, status=ERROR): from `PayrexxResponse.message` via `ConsumerServiceClientErrorException(response.getMessage())` -> `TransactionTask` catch (`e.getMessage()`, since that exception carries no cause). Pattern: `"An error occurred: <reason>."` e.g. "An error occurred: Your card has expired." The `"An error occurred: "` prefix is added by Payrexx, not Klara. **Verified (grep):** the string appears in luz_online_payment only in a doc comment (`PayrexxResponse.java:23`) and a test mock (`PayrexxCommunicatorTest.java:30`) — no `src/main` code emits or concatenates it, proving it is external/pass-through.
2. **JVM/framework exception text** (status=TECHNICAL_ERROR): WebApplicationException / ProcessingException timeout / NPE / the `ChargeTransactionService.exceptionally` executor failure. Runtime-generated, e.g. "HTTP 500 Internal Server Error".
3. **One repo-defined literal** (status=TECHNICAL_ERROR): `IllegalStateException("Can not charge without Transaction Id")` / `"Can not refund without Transaction Id"` thrown inside the try at `TransactionTask.java:30/58` when `request.getId()` is null. The ONLY message string literally defined in luz_online_payment.

On success the converter never sets message -> null.

Subtlety: the charge/refund methods in `TransactionRestCallerV2` are NOT annotated `@ConsumerApiErrorHandled` (only `getTransactionsWithinTimeRange` is), so `ConsumerApiErrorHandledInterceptor` does not wrap them — which is why Payrexx prose reaches `TransactionTask` clean via `e.getMessage()` rather than through a wrapped `getCause()`.

**Consequence for the taxonomy:** the decline reason is free-form English authored by Payrexx, not an enumerable set of code-keyed constants -> cannot be reliably switched on. Reinforces introducing a structured failureCategory at this boundary.

Repo: luz_online_payment. Ticket: LUZ-157476.

## Related

- [[1 Projects/luz_store/LUZ-157476/Payrexx card declines reach luz_store as ERROR with prose, not DECLINED]]
- [[luz_online_payment silently drops Payrexx decline codes]]
