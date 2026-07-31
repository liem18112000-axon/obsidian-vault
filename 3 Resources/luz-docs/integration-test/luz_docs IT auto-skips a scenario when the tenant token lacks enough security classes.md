---
title: "luz_docs IT auto-skips a scenario when the tenant token lacks enough security classes"
created: 2026-06-15
type: howto
status: seedling
source: "session 2026-06-15"
tags: [luz-docs, integration-test, behave, security-class, skip]
---

# luz_docs IT auto-skips a scenario when the tenant token lacks enough security classes

`features/environment.py` `before_scenario` computes the highest `$SCn` index a scenario needs and skips the scenario when the tenant token carries fewer codes — so "skip when the tenant's security classes don't meet the number needed" is achieved purely by writing `$SCn` placeholders.

How it counts: `_required_security_classes(scenario)` scans step text, step tables, AND any `*.json` case file named in a step (regex `"([^"]+\.json)"`), running `security_class_util.max_placeholder_index` over each. `_available_security_classes` mints one cron token per tenant and counts its `security_classes` claim. If available < required it calls `scenario.skip(...)`. A transient token-mint failure returns None and the scenario runs rather than being skipped.

Practical consequence: to make a new materialize scenario auto-skip on under-provisioned tenants, just reference the codes as `$SCn` in the case JSON — no extra wiring needed.

Related: [[luz_docs IT: $SCn security-class placeholders resolve against the tenant token claim]]

## Related

- [[luz_docs IT: $SCn security-class placeholders resolve against the tenant token claim]]
