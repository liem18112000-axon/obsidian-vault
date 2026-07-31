---
ai_hash: b1b374874501ebf3
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-31
entities:
- luz_online_payment notify webhook
- Payrexx webhooks
- dev environment
- GKE luz-online-payment
- Undertow access logs
- POST /luz_online_payment/api/transactions/notify
- HTTP 400
- HTTP 200
- HTTP 404
- JAX-RS bean-validation rejection
- NotifiedTransactionConsumerFactory
- '@Valid layer'
- Payrexx
- Transaction
- amount
- status
- payment
- invoice
- TransactionStatus.from
- OrderGateway
- luz_store
- LUZ-157476
- DECLINED webhook
- metadata.decline_code
- PayrexxCommunicatorV2
- online-dev.klara.tech
- Payrexx notify webhook dispatches to two consumers, neither forwards decline code
- luz_online_payment Payrexx webhook uses JSON-only Jackson mapper with FAIL_ON_UNKNOWN_PROPERTIES
  off
- Payrexx delivers decline code only via webhook, not sync response
- TransactionStatus.from returns null on unknown Payrexx status causing silent NotNull
  400
source: dev GKE access logs 2026-07-31
status: seedling
tags:
- payrexx
- webhook
- luz-online-payment
- luz-157476
- bug
- validation
- dev-logs
title: luz_online_payment notify webhook silently 400-rejects ~43% of Payrexx webhooks
  on dev
type: observation
---

# luz_online_payment notify webhook silently 400-rejects ~43% of Payrexx webhooks on dev

Measured on dev (GKE `luz-online-payment`, Undertow access logs, 7-day window, 2026-07): `POST /luz_online_payment/api/transactions/notify` returned **200 x140, 400 x105, 404 x7**. So ~43% of real Payrexx webhooks (105/245 non-bot) are rejected with HTTP 400.

The 400s are a SILENT JAX-RS bean-validation rejection, not a business error:
- No exception is logged (7-day search for ConstraintViolation / ValidationException / "Not found gateway" found nothing).
- The `NotifiedTransactionConsumerFactory` dispatch INFO log never fires for them -> the request dies at the `@Valid` layer before `handle()` runs.
- They come in retry PAIRS ~2s apart = Payrexx re-sending after the non-2xx (Payrexx retries on non-2xx, then gives up).
- 200s have empty body (`bytes-sent=-`, `Response.ok()`); 400s are ~156-157 bytes (small RESTEasy violation JSON). 200s and 400s coexist -> payload-shape-dependent.

Root cause = one of the four top-level `@NotNull` fields on `Transaction` (`amount`, `status`, `payment`, `invoice`) being null on a class of webhooks. `Payment`/`Invoice` have no inner constraints so the `@Valid` cascade is not the trigger. Prime suspects: `payment` absent on declined/non-card events, or `status` null (see [[TransactionStatus.from returns null on unknown Payrexx status causing silent NotNull 400]]).

Impact: those ~105 webhooks never update OrderGateway nor reach luz_store. Critically for LUZ-157476, the DECLINED webhook (carrying `metadata.decline_code`) is very likely among the dropped 400s, so the decline code is lost before it can be read. Not-yet-pinned: the exact null field (app logs neither request body nor 400 response body) -- capture one real 400 payload (ask Payrexx / add a temp request-logging filter + ConstraintViolationException mapper on dev).

Also confirmed here: the synchronous charge decline logs `Payrexx error: An error occurred: Your card has expired.` (PayrexxCommunicatorV2) with NO code -- matches the sync-path finding. Endpoint is public at `online-dev.klara.tech`, `@PermitAll`, no signature (WordPress-scanner bots hit it).

Related: [[Payrexx notify webhook dispatches to two consumers, neither forwards decline code]], [[luz_online_payment Payrexx webhook uses JSON-only Jackson mapper with FAIL_ON_UNKNOWN_PROPERTIES off]], [[Payrexx delivers decline code only via webhook, not sync response]].

## Related

- [[1 Projects/luz_online_payment/Payrexx notify webhook dispatches to two consumers, neither forwards decline code]]
- [[TransactionStatus.from returns null on unknown Payrexx status causing silent NotNull 400]]

%% ai-graph-start %%

**Related notes:**
- [[Payrexx notify webhook dispatches to two consumers, neither forwards decline code]]
- [[KlaraPay DTOs are code-blind - lenient Jackson drops any Payrexx decline code]]
- [[Payrexx card declines reach luz_store as ERROR with prose, not DECLINED]]
- [[TransactionStatus.from returns null on unknown Payrexx status causing silent NotNull 400]]
- [[luz_online_payment Payrexx webhook uses JSON-only Jackson mapper with FAIL_ON_UNKNOWN_PROPERTIES off]]

**Relations:**
- luz_online_payment notify webhook — *rejects* — Payrexx webhooks
- luz_online_payment notify webhook — *operates on* — dev environment
- GKE luz-online-payment — *hosts* — luz_online_payment notify webhook
- Undertow access logs — *measure* — POST /luz_online_payment/api/transactions/notify
- POST /luz_online_payment/api/transactions/notify — *returns* — HTTP 200
- POST /luz_online_payment/api/transactions/notify — *returns* — HTTP 400
- POST /luz_online_payment/api/transactions/notify — *returns* — HTTP 404
- HTTP 400 — *is a type of* — JAX-RS bean-validation rejection
- JAX-RS bean-validation rejection — *occurs at* — @Valid layer
- @Valid layer — *prevents dispatch by* — NotifiedTransactionConsumerFactory
- Payrexx — *sends* — Payrexx webhooks
- Payrexx webhooks — *are processed by* — luz_online_payment notify webhook
- Transaction — *has field* — amount
- Transaction — *has field* — status
- Transaction — *has field* — payment
- Transaction — *has field* — invoice
- amount — *is a @NotNull field on* — Transaction
- status — *is a @NotNull field on* — Transaction
- payment — *is a @NotNull field on* — Transaction
- invoice — *is a @NotNull field on* — Transaction
- TransactionStatus.from — *can return null for* — status
- status — *being null causes* — HTTP 400
- HTTP 400 — *prevents update of* — OrderGateway
- HTTP 400 — *prevents update of* — luz_store
- DECLINED webhook — *is among* — HTTP 400
- DECLINED webhook — *carries* — metadata.decline_code
- metadata.decline_code — *is lost for* — LUZ-157476
- PayrexxCommunicatorV2 — *logs* — Payrexx error: An error occurred: Your card has expired.
- online-dev.klara.tech — *is public endpoint for* — luz_online_payment notify webhook
- luz_online_payment notify webhook — *is related to* — Payrexx notify webhook dispatches to two consumers, neither forwards decline code
- luz_online_payment notify webhook — *is related to* — luz_online_payment Payrexx webhook uses JSON-only Jackson mapper with FAIL_ON_UNKNOWN_PROPERTIES off
- luz_online_payment notify webhook — *is related to* — Payrexx delivers decline code only via webhook, not sync response
- TransactionStatus.from returns null on unknown Payrexx status causing silent NotNull 400 — *is a cause of* — HTTP 400

%% ai-graph-end %%