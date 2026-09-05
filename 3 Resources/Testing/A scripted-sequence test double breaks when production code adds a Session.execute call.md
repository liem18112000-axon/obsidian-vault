---
title: "A scripted-sequence test double breaks when production code adds a Session.execute call"
created: 2026-08-20
type: lesson
status: seedling
source: "session 2026-08-20, leo-customer360 commit 4da1868"
tags: [testing, test-doubles, sqlalchemy, mocking, gotcha]
---

# A scripted-sequence test double breaks when production code adds a Session.execute call

A test double that scripts a **fixed sequence of return values** for `Session.execute(...)` (one canned result per call, in order) is silently coupled to the exact number and order of `execute` calls the production code makes. Adding **any** new `Session.execute` statement to that code path — even an unrelated side-effect like setting a session variable — consumes one scripted result out of position and shifts every subsequent assertion.

**Symptom cluster** (all from one added statement): off-by-one wrong results, count mismatches (`assert 4 == 3`), and `KeyError` on parameter dicts that are now read against the wrong call. In `customer360-api` this broke 11 tests at once when a leading `set_config` was added to login provisioning.

**Two takeaways:**
1. Keep genuinely out-of-band SQL off the ORM session so it never enters the scripted sequence — e.g. run it on the raw connection (see [[Set Postgres RLS session GUC via the raw DBAPI connection, not Session.execute]]).
2. Prefer test doubles that match on the SQL/statement rather than blindly popping the next scripted result — positional scripts are brittle to any new statement.

Root cause was found via `git log`/blame on the SQL sequence: the assertions had been stable across two prior refactors and only the commit that inserted the extra `Session.execute` broke them — evidence the code regressed, not the tests.

## Related

- [[Set Postgres RLS session GUC via the raw DBAPI connection]]
- [[not Session.execute]]
