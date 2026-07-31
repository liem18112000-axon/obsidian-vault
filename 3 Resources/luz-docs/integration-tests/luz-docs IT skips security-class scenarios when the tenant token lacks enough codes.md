---
title: "luz-docs IT skips security-class scenarios when the tenant token lacks enough codes"
created: 2026-06-15
type: howto
status: seedling
source: "session 2026-06-15"
tags: [luz-docs, integration-test, behave, security-class]
---

# luz-docs IT skips security-class scenarios when the tenant token lacks enough codes

The luz_docs_integration_test suite skips (not fails) any scenario that needs more security classes than the tenant token carries. Implemented in `features/environment.py` `before_scenario`.

**How the required count is derived (`_required_security_classes`):** scan every step for the highest `$SCn` index, across:
- `step.name` text,
- `step.text` (multiline),
- `step.table` headings + cells,
- any `*.json` filename referenced in `step.name` — the file is located under `resources/test-data/` and its content scanned too (covers materialize case files whose codes live in JSON, not the feature).

**Key behave fact that makes this work:** for a Scenario Outline, behave substitutes the Examples-row values into `step.name` on the generated scenario, so `run materialize case from "<configFile>"` already shows the concrete filename at `before_scenario` time.

**Available count:** `_available_security_classes` mints a cron access token for the tenant (cached per tenant id), decodes its `security_classes` claim, and counts it. On a token-mint error it returns None → scenario runs rather than being wrongly skipped.

If `available < required`, call `scenario.skip("requires N security classes but tenant token carries only M")`.

**Why skip rather than fail:** a tenant that simply lacks enough seeded classes is an environment limitation, not a regression — failing would create noise. The resolverّs out-of-range assert is the backstop if a scenario slips through unskipped.

Related: [[luz-docs IT $SCn security-class placeholders resolve against the token claim]]

## Related

- [[luz-docs IT $SCn security-class placeholders resolve against the token claim]]
