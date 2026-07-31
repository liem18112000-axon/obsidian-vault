---
ai_hash: 1e4e353eb9b13f07
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-31
entities: []
source: LUZ-157476, email from Payrexx (Ugur) 2026-07
status: seedling
tags:
- payrexx
- webhook
- decline-code
- luz-157476
- gotcha
title: Payrexx delivers decline code only via webhook, not sync response
type: observation
---

# Payrexx delivers decline code only via webhook, not sync response

Payrexx returns the ISO 8583 decline/error code **only in the transaction webhook** it POSTs to your server. The synchronous charge/GET response for a failed charge carries no code — it is just `{"status":"error","message":"..."}`. So you cannot obtain the code by polling or reading the charge response; you must consume the webhook.

Confirmed 2026-07 by a Payrexx developer (Ugur) via email plus a sample webhook payload, during LUZ-157476.

Consequence: any decline-code plumbing wired onto the synchronous path (e.g. via the charge response / a client-error exception) will always see `null`. The code must be read on the webhook path instead.

See [[Payrexx decline code lives at transaction.metadata.decline_code]] and [[DeclineCodes resolver misses nested metadata.decline_code]].

## Related

- [[Payrexx decline code lives at transaction.metadata.decline_code]]
- [[DeclineCodes resolver misses nested metadata.decline_code]]

%% ai-graph-start %%

**Related notes:**
- [[Payrexx decline code lives at transaction.metadata.decline_code]]
- [[DeclineCodes resolver misses nested metadata.decline_code]]
- [[Payrexx v1.0 charge API returns only status+message on failure — no ISO 8583 code]]
- [[Payrexx publishes no catalog of API wrapper error messages]]
- [[luz_online_payment silently drops Payrexx decline codes]]

%% ai-graph-end %%