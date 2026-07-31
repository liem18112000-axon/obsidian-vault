---
title: "luz_store invoice bundles are Latin-1 - write unicode escapes and doubled apostrophes"
created: 2026-07-24
type: lesson
status: budding
source: "LUZ-157476 implementation 2026-07-24"
tags: [luz-store, i18n, properties, encoding, gotcha]
---

# luz_store invoice bundles are Latin-1 - write unicode escapes and doubled apostrophes

The message/invoice/text_*.properties bundles in luz_store are legacy Latin-1-encoded (existing umlauts are raw 0xFC bytes). When adding keys: (1) write ALL non-ASCII as \uXXXX escapes — appending UTF-8 chars creates mixed-encoding mojibake under Java 8 ResourceBundle; (2) in patterns that use MessageFormat placeholders ({0},{1}), double every apostrophe ('') or French/Italian text silently loses letters (the pre-existing FR generic key has this bug: n'a renders as na); (3) plain-text keys without placeholders keep single apostrophes.

Category-key fallback pattern used for LUZ-157476: bundle.containsKey(prefix+category) ? getString : explicit fallback — lets the enum grow without requiring all locales to be translated first. Shell gotcha hit twice: perl replacement '\u00fc' loses the backslash (\u is titlecase escape in perl replacements) — use \x5cu00fc instead.

## Related
- [[LUZ-157476 maps failure categories in luz_store only, overriding the boundary recommendation]]
- [[Write-time localization into the existing message column avoids schema change]]
