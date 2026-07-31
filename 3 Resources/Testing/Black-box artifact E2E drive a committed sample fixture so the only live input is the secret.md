---
title: "Black-box artifact E2E: drive a committed sample fixture so the only live input is the secret"
created: 2026-06-14
type: lesson
status: seedling
source: "fb-info-project, 2026-06-14"
tags: [testing, e2e, black-box, ci, design-decision]
---

# Black-box artifact E2E: drive a committed sample fixture so the only live input is the secret

When E2E tests treat a built artifact as a black box (drive the exe via subprocess, **never import the SUT's source**), feed them a *committed* input fixture rather than fabricating inputs from config. Two payoffs:

1. **Minimal live config.** If the CLI already defaults its input to a repo file (here `fb-scraper` defaults `batch --input` to `resources/sample.xlsx`), the live test copies that committed workbook into its temp CWD and runs with the default. The links/cases come from the fixture, so the *only* thing the live run needs supplied is the secret (a real Facebook session). That makes 'skip when the secret is absent' the single clean gate — no per-case URL list to manage.

2. **Tests exactly what ships.** The artifact runs against the same sample file that ships in the repo, not a synthetic one.

**Gotcha / deliberate duplication:** because the black-box contract forbids importing `src/`, a test that needs to know something about the input (e.g. how many links the sheet holds, to assert one output per link) must *re-derive* it with a small local helper that mirrors the SUT's parser (`excel_io.read_input`). That duplication is intentional and correct — reusing the SUT's own parser would couple the test to the code it's supposed to validate independently. A reuse-linter flagging it is a false positive here.

Context: fb-info-project `test/test_modes_live.py` + CI live tier in `build-exe.yml`.

## Related

- [[Gate a GitHub Actions step on a secret's presence via env]]
- [[not if:]]
