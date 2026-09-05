---
title: "MessageFormat apostrophe doubling in luz_store invoice message properties"
created: 2026-08-31
type: lesson
status: seedling
source: "session 2026-08-31"
tags: [luz-store, i18n, messageformat, gotcha, LUZ-157476]
---

# MessageFormat apostrophe doubling in luz_store invoice message properties

In luz_store, the invoice notification message bundles at `src/main/resources/message/invoice/text_*.properties` split into two key families that need **opposite apostrophe escaping**, because they are consumed differently.

- `payment_by_credit_card_charge_failed_*` (email/notification text) is always rendered through `MessageFormat.format(template, brandName, cardNumber)` — even the entries that carry no `{0}`/`{1}` placeholder still get args passed. So every literal apostrophe MUST be doubled (`''`), or MessageFormat swallows it (single `'` starts a quoted section).
- `payment_failure_message_*` (in-app text) is returned **verbatim**, no MessageFormat. So apostrophes stay single (`'`); doubling them would print a literal `''`.

The switch lives in `FailureCategory.resolveBundleText(...)`:
```java
return args.length == 0 ? template : MessageFormat.format(template, args);
```
The email path calls it with `(brandName, cardNumber)` args; the in-app path calls it with none.

Practical gotcha when populating official translations from a spreadsheet: FR/IT come with apostrophes (and IT sometimes a curly U+2019). Normalize U+2019 to a straight `'`, then **double apostrophes only in the email family**, keep single in the in-app family. Verify with a tiny Java `MessageFormat.format` round-trip — the doubled `''` should render as a single `'`.

File format: ISO-8859-1 + CRLF, non-ASCII written as `\uXXXX` escapes (matching the existing taxonomy block). Java `Properties.load` reads ISO-8859-1 and resolves the escapes, exactly like `ResourceBundle`.

Context: LUZ-157476 failure-reason taxonomy.

## Related

- [[Java properties files are ISO-8859-1 with unicode escapes]]
