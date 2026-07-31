---
title: "Prod card declines reach luz_store as ERROR plus Payrexx prose, not DECLINED"
created: 2026-07-23
type: observation
status: budding
source: "luz_online_payment code verification 2026-07-23"
tags: [luz-store, payrexx, klarapay, gotcha]
---

# Prod card declines reach luz_store as ERROR plus Payrexx prose, not DECLINED

Verified in luz_online_payment source: real production card declines reach luz_store as status=ERROR with Payrexx prose ('An error occurred: ...'), NOT as status=DECLINED. Payrexx signals charge failure on the direct Transaction API via wrapper status='error' + message; KlaraPay throws ConsumerServiceClientErrorException(response.getMessage()) (PayrexxCommunicatorV2.java:45-48), and TransactionTask remaps that to ERROR with the prose as message (TransactionTask.java:38-50). An in-band 'declined' transaction passes through 1:1 BUT the converter never sets message (KlaraTransactionRequestConverter.java:10-15) — DECLINED always arrives with message=null.

This explains why the DECLINED fall-through gap in luz_store went unnoticed: the ERROR branch does all the visible work in production. Decline-taxonomy input at luz_store = the ERROR prose, not the DECLINED status.

## Related
- [[DECLINED status falls through invoice charge-failure handling in luz_store]]
- [[LUZ-157476 decline taxonomy maps codes at luz_online_payment boundary]]
