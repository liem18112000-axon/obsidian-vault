---
title: "Payrexx card declines reach luz_store as ERROR with prose, not DECLINED"
created: 2026-07-24
type: lesson
status: seedling
source: "session 2026-07-24, code investigation"
tags: [luz-online-payment, payrexx, klarapay, gotcha, LUZ-157476]
---

# Payrexx card declines reach luz_store as ERROR with prose, not DECLINED

Real production card declines from Payrexx do **not** arrive at luz_store as `TransactionStatus.DECLINED`. They arrive as `status=ERROR` carrying Payrexx's own prose (e.g. "An error occurred: Your card has expired.") in the `message` field.

Path: Payrexx returns wrapper `PayrexxResponse.status="error"` -> `PayrexxCommunicatorV2.handleResponse` throws `ConsumerServiceClientErrorException(response.getMessage())` -> `TransactionTask` catch block sets `request.setMessage(cause.getMessage())` and `status=ERROR` (charge) / `REFUND_FAILED` (refund).

A **true in-band `DECLINED`** takes the *success* path instead: `KlaraTransactionRequestConverter.convertToKlaraTransactionRequest` builds the result with only companyUri/invoiceAmt/status/id — it **never sets `message`** — so a DECLINED reaches luz_store with `message = null`.

**Why it matters:** this asymmetry is exactly why luz_store's controller `DECLINED` branch was never exercised and the decline-handling gap (LUZ-157476) went unnoticed — the visible work always happened on the ERROR branch. When building the decline taxonomy, handle both shapes.

**Return-path mechanics (confirmed 2026-07-24):** `KlaraTransactionRequest.message` is a response-only, error-only field, set in exactly 3 places — all failures:
1. `TransactionTask.chargeTransaction` catch (`cause.getMessage()` else `getMessage()`; status ERROR/TECHNICAL_ERROR)
2. `TransactionTask.refundTransaction` catch (status REFUND_FAILED/TECHNICAL_ERROR)
3. `ChargeTransactionService.chargeTransactionAsynchronous` / `refundTransactionAsynchronous` `.exceptionally` fallback (`throwable.getMessage()`; status TECHNICAL_ERROR) — only if the async executor future itself blows up.

Object-identity subtlety: the **success path returns a brand-new object** from the converter (message untouched -> null), while **error paths mutate and return the incoming request** (keeps id/invoiceAmt/invoiceItemId/companyUri, adds message + status). The REST layer (`TransactionAsyncResource.chargeTransaction`) then serializes the list as HTTP 200 JSON, filtering out null entries.

Repo: luz_online_payment. Ticket: LUZ-157476 / epic LUZ-156281.

## Related

- [[luz_online_payment silently drops Payrexx decline codes]]
- [[Map decline codes to failureCategory at the KlaraPay boundary, not by prose-parsing in luz_store]]
