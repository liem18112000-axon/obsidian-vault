---
title: "Payrexx card declines reach luz_store as ERROR with prose, not DECLINED"
aliases:
  - "Prod card declines reach luz_store as ERROR plus Payrexx prose, not DECLINED"
created: 2026-07-23
type: lesson
status: budding
source: "luz_online_payment code verification 2026-07-23/24"
tags: [luz-online-payment, luz-store, payrexx, klarapay, gotcha, LUZ-157476]
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
