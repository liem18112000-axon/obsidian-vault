---
ai_hash: bb9087f50c7ce65d
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-24
entities:
- KlaraTransactionRequest
- message
- Payrexx prose
- runtime exception text
- mapped constant
- luz_online_payment
- PayrexxResponse.message
- ConsumerServiceClientErrorException
- TransactionTask
- e.getMessage()
- Payrexx
- ERROR
- JVM/framework exception text
- TECHNICAL_ERROR
- WebApplicationException
- ProcessingException
- timeout
- NPE
- ChargeTransactionService.exceptionally
- repo-defined literal
- IllegalStateException
- Transaction Id
- request.getId()
- TransactionRestCallerV2
- '@ConsumerApiErrorHandled'
- ConsumerApiErrorHandledInterceptor
- getTransactionsWithinTimeRange
- decline reason
- structured failureCategory
- LUZ-157476
- luz_store
- Payrexx card declines
- Payrexx decline codes
- setMessage(...)
- charge/refund methods
source: session 2026-07-24, code investigation
status: seedling
tags:
- luz-online-payment
- payrexx
- gotcha
- LUZ-157476
title: KlaraTransactionRequest.message content is Payrexx prose or runtime exception
  text, never a mapped constant
type: lesson
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

%% ai-graph-start %%

**Related notes:**
- [[Payrexx card declines reach luz_store as ERROR with prose, not DECLINED]]
- [[LUZ-157476 decline taxonomy maps codes at luz_online_payment boundary]]
- [[Payrexx v1.0 charge API returns only status+message on failure — no ISO 8583 code]]
- [[KlaraPay V2 Java classes still call Payrexx API v1.0 on the consumer flow]]
- [[LUZ-157476 decline-code flow luz-online-payment forwards, luz_store maps]]

**Relations:**
- KlaraTransactionRequest — *HAS_FIELD* — message
- message — *CONTAINS* — Payrexx prose
- message — *CONTAINS* — runtime exception text
- message — *IS_NEVER* — mapped constant
- message — *IS_ASSIGNED_BY* — setMessage(...)
- setMessage(...) — *PASSES* — dynamic exception text
- Payrexx prose — *ORIGINATES_FROM* — PayrexxResponse.message
- ConsumerServiceClientErrorException — *USES* — response.getMessage()
- TransactionTask — *CATCHES* — e.getMessage()
- Payrexx — *ADDS_PREFIX* — An error occurred: 
- Payrexx prose — *HAS_STATUS* — ERROR
- JVM/framework exception text — *HAS_STATUS* — TECHNICAL_ERROR
- JVM/framework exception text — *INCLUDES* — WebApplicationException
- JVM/framework exception text — *INCLUDES* — ProcessingException
- JVM/framework exception text — *INCLUDES* — timeout
- JVM/framework exception text — *INCLUDES* — NPE
- JVM/framework exception text — *INCLUDES* — ChargeTransactionService.exceptionally
- repo-defined literal — *HAS_STATUS* — TECHNICAL_ERROR
- repo-defined literal — *IS_AN* — IllegalStateException
- IllegalStateException — *IS_THROWN_IN* — TransactionTask
- IllegalStateException — *REQUIRES* — Transaction Id
- IllegalStateException — *IS_THROWN_WHEN* — request.getId() IS_NULL
- message — *IS_NULL_ON* — success
- charge/refund methods — *ARE_NOT_ANNOTATED_WITH* — @ConsumerApiErrorHandled
- charge/refund methods — *ARE_IN* — TransactionRestCallerV2
- ConsumerApiErrorHandledInterceptor — *DOES_NOT_WRAP* — charge/refund methods
- Payrexx prose — *REACHES* — TransactionTask
- Payrexx prose — *REACHES_VIA* — e.getMessage()
- decline reason — *IS_A* — free-form English
- decline reason — *IS_AUTHORED_BY* — Payrexx
- decline reason — *CANNOT_BE* — reliably switched on
- structured failureCategory — *IS_RECOMMENDED_FOR* — decline reason
- luz_online_payment — *IS_REPOSITORY_FOR* — KlaraTransactionRequest
- LUZ-157476 — *IS_TICKET_FOR* — luz_online_payment
- LUZ-157476 — *IS_RELATED_TO* — Payrexx card declines reach luz_store as ERROR with prose, not DECLINED
- luz_online_payment — *DROPS* — Payrexx decline codes
- PayrexxResponse.message — *IS_REFERENCED_IN* — luz_online_payment (doc comment)
- PayrexxCommunicatorTest.java:30 — *IS_REFERENCED_IN* — luz_online_payment (test mock)
- TransactionTask — *IS_IN* — luz_online_payment
- ChargeTransactionService — *IS_IN* — luz_online_payment
- TransactionRestCallerV2 — *IS_IN* — luz_online_payment
- ConsumerApiErrorHandledInterceptor — *IS_IN* — luz_online_payment
- getTransactionsWithinTimeRange — *IS_METHOD_OF* — TransactionRestCallerV2
- luz_store — *RECEIVES* — Payrexx card declines
- Payrexx card declines — *ARE_REPORTED_AS* — ERROR
- Payrexx card declines — *INCLUDE* — prose
- Payrexx card declines — *ARE_NOT* — DECLINED (status)

%% ai-graph-end %%