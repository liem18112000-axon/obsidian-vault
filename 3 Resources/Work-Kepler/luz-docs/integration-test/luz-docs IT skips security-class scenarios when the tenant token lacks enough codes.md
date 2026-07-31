---
title: "luz-docs IT skips security-class scenarios when the tenant token lacks enough codes"
created: 2026-06-15
type: howto
status: seedling
source: "session 2026-06-15"
tags: [luz-docs, integration-test, behave, security-class, skip]
---

# luz-docs IT skips security-class scenarios when the tenant token lacks enough codes

`features/environment.py` `before_scenario` **skips** (never fails) any scenario needing more security classes than the tenant token carries. Writing `$SCn` placeholders is the *only* wiring needed — no extra per-scenario setup.

**Required count (`_required_security_classes`)** — highest `$SCn` index found via `security_class_util.max_placeholder_index` across:
- `step.name`, `step.text` (multiline), `step.table` headings + cells,
- any `*.json` filename referenced in `step.name` (regex `"([^"]+\.json)"`), located under `resources/test-data/` and scanned too — this is what covers materialize case files whose codes live in JSON, not in the feature.

**Why the JSON scan works on outlines:** behave substitutes Examples-row values into `step.name` before `before_scenario`, so `run materialize case from "<configFile>"` already shows the concrete filename.

**Available count (`_available_security_classes`):** mints a cron access token per tenant (cached by tenant id), decodes and counts the `security_classes` claim. A transient mint failure returns `None` → the scenario runs rather than being wrongly skipped.

If `available < required` → `scenario.skip("requires N security classes but tenant token carries only M")`. Skip rather than fail because an under-provisioned tenant is an environment limitation, not a regression; the resolver's out-of-range assert is the backstop if a scenario slips through.

## Related

- [[luz-docs IT $SCn security-class placeholders resolve against the token claim]]
