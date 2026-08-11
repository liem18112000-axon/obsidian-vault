---
ai_hash: 39f86530be44c383
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-16
entities: []
source: session 2026-06-16
status: evergreen
tags:
- luz-docs
- integration-test
- behave
- security-class
- gotcha
title: luz-docs IT before_scenario reads but does not substitute $SCn into step text
type: gotcha
---

# luz-docs IT before_scenario reads but does not substitute $SCn into step text

In the luz-docs IT suite, `environment.before_scenario` **reads** each step`s text to compute the `$SCn` skip threshold (via `security_class_util.max_placeholder_index(step.name)`), but it does **not substitute** `$SCn` into the step text. 

Consequence: resolving a `$SCn` placeholder must happen **inside each step body** (e.g. `security_class_util.resolve` / `resolve_csv` on the parsed argument). Converting a feature file to `$SCn` without also updating its step definitions leaves the literal `"$SC1"` to be parsed as the argument and sent to the server verbatim. (Exception already in place: Scenario-Outline `<configFile>` tokens ARE substituted into `step.name` so the skip guard can scan referenced materialize JSON — but that is example-table substitution by behave, not `$SCn` resolution.)

Related: [[luz-docs IT $SCn authorization failures were deterministic resolution regressions, not membership]] [[luz-docs IT $SCn security-class placeholders resolve against the token claim]]

## Related

- [[luz-docs IT $SCn security-class placeholders resolve against the token claim]]

%% ai-graph-start %%

**Related notes:**
- [[luz-docs IT $SCn security-class placeholders resolve against the token claim]]
- [[luz-docs IT skips security-class scenarios when the tenant token lacks enough codes]]
- [[luz-docs IT $SCn authorization failures were deterministic resolution regressions, not membership]]
- [[behave step patterns differing only by quote style are distinct definitions]]
- [[JSON-driven Scenario Outline pattern for luz_docs materialize integration tests]]

%% ai-graph-end %%