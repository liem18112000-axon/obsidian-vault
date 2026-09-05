---
title: "FailureCategoryTest hard-codes rendered i18n strings so properties edits break it"
created: 2026-09-03
type: lesson
status: seedling
source: "session 2026-09-03 LUZ-157476"
tags: [luz_store, i18n, testing, gotcha, LUZ-157476, cloud-build]
---

# FailureCategoryTest hard-codes rendered i18n strings so properties edits break it

FailureCategoryTest (`ch.klara.luz.store.onlinepayment.model`) asserts on the **fully-rendered** customer-facing payment-failure strings — per `Locale` and per `DeliveryChannel` — via hard-coded `assertEquals` expectations. Because the expected text is the resolved message, **any** edit to a value in `src/main/resources/message/invoice/text_{de,fr,it,en}.properties` silently breaks these unit tests until the matching assertion is updated in the same commit.

**Why it bites:** the strings live in two places (the `.properties` file and the test literal) with no compile-time link. A translator-style reword compiles fine and only fails at the surefire `test` phase — and in the Cloud Build (`klara-infra`, trigger `luz-store`) `mvn deploy` runs tests first, so a red test means the container image is **never published** and master ships nothing.

**Concrete incident (LUZ-157476):** commit `9f751294a` rewrote the DE messages from informal *du* to formal *Sie* form and merged to master (PR #1845). That turned the master build red with 3 failures — `buildMessageGermanLocaleRendersGermanDeclined` / `...EmailRendersGermanExpired` / `...RendersGermanNoPaymentMethod` (test lines 138/148/158). Fix branch: `mt-receive/LUZ-157476-fix-failure-category-test`.

**How to apply:** whenever you touch an invoice `text_*.properties` value, `grep FailureCategoryTest` for the affected message and update its expected literal in the same commit. Verify locally with `mvn -o test -Dtest=FailureCategoryTest` before pushing. Note the EN and DE/FR/IT assertions are independent — changing only DE (leaving EN unchanged on master) breaks only the German tests.

**Placeholder gotcha:** the templates embed `{0} {1}` (brand, card number). When the request carries no payment, both render empty, so `"...Karte {0} {1} konnte..."` collapses to `"...Karte   konnte..."` with the extra literal spaces still present. The expected string in the test must keep those spaces verbatim or the assertion fails on whitespace alone.

Related: [[payrexx-iso8583-decline-code]], LUZ-157476 payment-failure taxonomy.

## Related

- [[payrexx-iso8583-decline-code]]
