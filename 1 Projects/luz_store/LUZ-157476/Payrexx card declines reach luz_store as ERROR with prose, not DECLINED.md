---
ai_hash: 01bf61bdc53e4a52
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
aliases:
- Prod card declines reach luz_store as ERROR plus Payrexx prose, not DECLINED
created: 2026-07-23
entities:
- Payrexx
- luz_store
- TransactionStatus.DECLINED
- ERROR
- prose
- message
- PayrexxResponse.status="error"
- PayrexxCommunicatorV2.handleResponse
- ConsumerServiceClientErrorException
- TransactionTask
- request message
- REFUND_FAILED
- KlaraTransactionRequestConverter.convertToKlaraTransactionRequest
- companyUri
- invoiceAmt
- status
- id
- 'null'
- asymmetry
- luz_store's DECLINED branch
- decline-handling gap
- LUZ-157476
- ERROR branch
- decline-taxonomy input
- taxonomy
- KlaraTransactionRequest.message
- TransactionTask.chargeTransaction catch
- TECHNICAL_ERROR
- TransactionTask.refundTransaction catch
- ChargeTransactionService.*Asynchronous .exceptionally fallback
- success path
- brand-new object
- error paths
- incoming request
- invoiceItemId
- TransactionAsyncResource.chargeTransaction
- list
- null entries
- luz_online_payment
- LUZ-156281
- Payrexx decline codes
- decline taxonomy
- codes
- luz_online_payment boundary
- invoice charge-failure handling
- Payrexx card declines
source: luz_online_payment code verification 2026-07-23/24
status: budding
tags:
- luz-online-payment
- luz-store
- payrexx
- klarapay
- gotcha
- LUZ-157476
title: Payrexx card declines reach luz_store as ERROR with prose, not DECLINED
type: lesson
---

# Payrexx card declines reach luz_store as ERROR with prose, not DECLINED

Real production card declines do **not** arrive at luz_store as `TransactionStatus.DECLINED`. They arrive as `status=ERROR` carrying Payrexx's own prose (e.g. "An error occurred: Your card has expired.") in `message`.

**Error path (what actually happens in prod):** Payrexx returns wrapper `PayrexxResponse.status="error"` → `PayrexxCommunicatorV2.handleResponse` (`:45-48`) throws `ConsumerServiceClientErrorException(response.getMessage())` → `TransactionTask` catch (`:38-50`) sets `request.setMessage(cause.getMessage())` and `status=ERROR` (charge) / `REFUND_FAILED` (refund).

**In-band DECLINED path:** takes the *success* path through `KlaraTransactionRequestConverter.convertToKlaraTransactionRequest` (`:10-15`), which builds the result from companyUri/invoiceAmt/status/id only and **never sets `message`** → DECLINED always arrives with `message = null`.

**Why it matters:** this asymmetry is why luz_store's DECLINED branch was never exercised and the decline-handling gap (LUZ-157476) went unnoticed — the ERROR branch does all the visible work. The decline-taxonomy input at luz_store is the ERROR prose, not the DECLINED status; a taxonomy must handle both shapes.

**Return-path mechanics:** `KlaraTransactionRequest.message` is response-only and error-only, set in exactly 3 places, all failures — `TransactionTask.chargeTransaction` catch (ERROR/TECHNICAL_ERROR), `TransactionTask.refundTransaction` catch (REFUND_FAILED/TECHNICAL_ERROR), and the `ChargeTransactionService.*Asynchronous` `.exceptionally` fallback (TECHNICAL_ERROR, only if the async executor future itself blows up).

Object-identity subtlety: the success path returns a **brand-new** object from the converter (message untouched → null), while error paths **mutate and return the incoming request** (keeps id/invoiceAmt/invoiceItemId/companyUri, adds message + status). `TransactionAsyncResource.chargeTransaction` then serializes the list as HTTP 200 JSON, filtering out null entries.

Repo: luz_online_payment. Ticket: LUZ-157476 / epic LUZ-156281.

## Related

- [[luz_online_payment silently drops Payrexx decline codes]]
- [[LUZ-157476 decline taxonomy maps codes at luz_online_payment boundary]]
- [[DECLINED status falls through invoice charge-failure handling in luz_store]]

%% ai-graph-start %%

**Related notes:**
- [[KlaraTransactionRequest.message content is Payrexx prose or runtime exception text, never a mapped constant]]
- [[LUZ-157476 decline taxonomy maps codes at luz_online_payment boundary]]
- [[Payrexx declines travel in-band on HTTP 2xx in the luz charge flow]]
- [[LUZ-157476 decline-code flow luz-online-payment forwards, luz_store maps]]
- [[DECLINED status falls through invoice charge-failure handling in luz_store]]

**Relations:**
- Payrexx card declines — *reach* — luz_store
- Payrexx card declines — *arrive as* — ERROR
- Payrexx card declines — *do not arrive as* — TransactionStatus.DECLINED
- ERROR — *carries* — prose
- prose — *is in* — message
- Payrexx — *returns* — PayrexxResponse.status="error"
- PayrexxResponse.status="error" — *causes* — PayrexxCommunicatorV2.handleResponse
- PayrexxCommunicatorV2.handleResponse — *throws* — ConsumerServiceClientErrorException
- TransactionTask — *catches* — ConsumerServiceClientErrorException
- TransactionTask — *sets* — request message
- TransactionTask — *sets status to* — ERROR
- TransactionTask — *sets status to* — REFUND_FAILED
- DECLINED path — *uses* — KlaraTransactionRequestConverter.convertToKlaraTransactionRequest
- KlaraTransactionRequestConverter.convertToKlaraTransactionRequest — *builds from* — companyUri
- KlaraTransactionRequestConverter.convertToKlaraTransactionRequest — *builds from* — invoiceAmt
- KlaraTransactionRequestConverter.convertToKlaraTransactionRequest — *builds from* — status
- KlaraTransactionRequestConverter.convertToKlaraTransactionRequest — *builds from* — id
- KlaraTransactionRequestConverter.convertToKlaraTransactionRequest — *never sets* — message
- TransactionStatus.DECLINED — *arrives with message* — null
- asymmetry — *causes* — luz_store's DECLINED branch
- luz_store's DECLINED branch — *was not exercised* — true
- asymmetry — *causes* — decline-handling gap
- decline-handling gap — *went unnoticed* — true
- decline-handling gap — *is* — LUZ-157476
- ERROR branch — *does* — visible work
- decline-taxonomy input — *at* — luz_store
- decline-taxonomy input — *is* — ERROR prose
- decline-taxonomy input — *is not* — TransactionStatus.DECLINED
- taxonomy — *must handle* — ERROR prose
- taxonomy — *must handle* — TransactionStatus.DECLINED
- KlaraTransactionRequest.message — *is* — response-only
- KlaraTransactionRequest.message — *is* — error-only
- KlaraTransactionRequest.message — *is set in* — TransactionTask.chargeTransaction catch
- TransactionTask.chargeTransaction catch — *sets status to* — ERROR
- TransactionTask.chargeTransaction catch — *sets status to* — TECHNICAL_ERROR
- KlaraTransactionRequest.message — *is set in* — TransactionTask.refundTransaction catch
- TransactionTask.refundTransaction catch — *sets status to* — REFUND_FAILED
- TransactionTask.refundTransaction catch — *sets status to* — TECHNICAL_ERROR
- KlaraTransactionRequest.message — *is set in* — ChargeTransactionService.*Asynchronous .exceptionally fallback
- ChargeTransactionService.*Asynchronous .exceptionally fallback — *sets status to* — TECHNICAL_ERROR
- success path — *returns* — brand-new object
- brand-new object — *from* — KlaraTransactionRequestConverter.convertToKlaraTransactionRequest
- brand-new object — *has message* — null
- error paths — *mutate* — incoming request
- error paths — *return* — incoming request
- incoming request — *keeps* — id
- incoming request — *keeps* — invoiceAmt
- incoming request — *keeps* — invoiceItemId
- incoming request — *keeps* — companyUri
- incoming request — *adds* — message
- incoming request — *adds* — status
- TransactionAsyncResource.chargeTransaction — *serializes* — list
- TransactionAsyncResource.chargeTransaction — *filters out* — null entries
- luz_online_payment — *is repo for* — LUZ-157476
- LUZ-157476 — *is ticket* — true
- LUZ-156281 — *is epic* — true
- LUZ-157476 — *is part of* — LUZ-156281
- luz_online_payment — *drops* — Payrexx decline codes
- LUZ-157476 — *concerns* — decline taxonomy
- decline taxonomy — *maps* — codes
- codes — *at* — luz_online_payment boundary
- TransactionStatus.DECLINED — *falls through* — invoice charge-failure handling
- invoice charge-failure handling — *in* — luz_store

%% ai-graph-end %%