---
title: "Build test fakes from verbatim production data, decoys included"
created: 2026-06-11
type: lesson
status: evergreen
source: "fb-info-project session 2026-06-11"
tags: [testing, fakes, regression, scraping]
---

# Build test fakes from verbatim production data, decoys included

A fake-DOM unit test passed while the same code failed on the real page, because the fake contained only the *target* element — not its neighbors. The real Facebook sort menu has a decoy: the "Newest" item's description contains the words "all comments", which an unanchored regex matched first. A fake holding only the "All comments" item can never catch that.

Lesson: when faking external UI/data for tests, copy **verbatim production strings for the whole neighborhood** (siblings, descriptions, decoys), not just the element under test. Selector and regex bugs are almost always *disambiguation* bugs — a fake without competing candidates tests nothing about disambiguation. After any live debugging session, feed the real strings you captured back into the fakes as regression data.

## Related

- [[Facebook's Newest sort option mentions 'all comments' in its description — anchor the label regex]]
