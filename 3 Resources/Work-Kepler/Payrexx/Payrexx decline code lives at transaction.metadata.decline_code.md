---
title: "Payrexx decline code lives at transaction.metadata.decline_code"
created: 2026-07-31
type: observation
status: seedling
source: "LUZ-157476 sample webhook payload 2026-07"
tags: [payrexx, webhook, decline-code, luz-157476]
---

# Payrexx decline code lives at transaction.metadata.decline_code

In a Payrexx transaction webhook, the decline code is **nested inside `metadata`**, at `transaction.metadata.decline_code` — not at the transaction top level. The value is **alphanumeric and ~2 chars** (e.g. `1A`), so treat it as a String, not an integer.

Raw sample body (form-urlencoded, PHP bracket notation), from LUZ-157476:

```
transaction[metadata][message]      = Strong Customer Authentication required
transaction[metadata][decline_code] = 1A
```

Note the sample above is form-urlencoded. Whether Klara actually receives form-encoded or JSON depends on how the webhook is registered (`type`) and must be verified against a real dev webhook — but either way the logical path to the code is the same: `transaction.metadata.decline_code`.

Related: [[Payrexx delivers decline code only via webhook, not sync response]], [[DeclineCodes resolver misses nested metadata.decline_code]].

## Related

- [[3 Resources/Work-Kepler/Payrexx/Payrexx delivers decline code only via webhook, not sync response]]
- [[DeclineCodes resolver misses nested metadata.decline_code]]
