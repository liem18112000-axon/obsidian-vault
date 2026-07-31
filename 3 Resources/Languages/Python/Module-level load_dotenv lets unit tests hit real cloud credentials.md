---
ai_hash: 188f93b7ced8b9fa
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-04
entities: []
source: session 2026-07-04
status: seedling
tags:
- python
- dotenv
- testing
- credentials
- footgun
title: Module-level load_dotenv lets unit tests hit real cloud credentials
type: lesson
---

# Module-level load_dotenv lets unit tests hit real cloud credentials

Gotcha (found 2026-07-04, appsflyer-data-connector): `config.py` calls `load_dotenv()` at **module import time**, so every pytest run silently inherits the real `.env` — including live cloud credentials. When the file sink became vStorage-only, two unit tests that called `run_day()` without injecting a sink built the REAL `S3JsonlSink` and **uploaded test objects to the production bucket** (they only "failed" because they asserted on a local path afterwards). Lessons: (1) any code-path default that constructs a cloud client is reachable from tests when dotenv loads at import — tests must always inject fakes for boundary objects (sinks, clients); (2) a module-level `load_dotenv()` is a footgun — prefer loading in entry points only (cli/main), or the test suite needs an autouse fixture that scrubs credential env vars; (3) after such a leak, clean the stray objects out of the bucket (delete_object), not just the test.

Related: [[Root main.py debug harness - stub only the boundary client]]

## Related

- [[Root main.py debug harness - stub only the boundary client]]

%% ai-graph-start %%

**Related notes:**
- [[MinIO server creds (ROOT_USERPASSWORD) are distinct env vars from the S3 client creds (ACCESS_KEYSECRET_KEY)]]
- [[Grep-audit env vars against code before pruning .env files]]
- [[Root main.py debug harness - stub only the boundary client]]
- [[AppsFlyer connector S3 config is vStorage-only - VSTORAGE_ env vars]]
- [[Dataclass field defaults reading env vars are evaluated at import time, not instantiation]]

%% ai-graph-end %%