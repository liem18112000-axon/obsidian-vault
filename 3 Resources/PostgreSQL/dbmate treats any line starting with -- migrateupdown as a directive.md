---
title: "dbmate treats any line starting with -- migrate:up/down as a directive"
created: 2026-08-24
type: lesson
status: seedling
source: "session 2026-08-24 dbmate implementation"
tags: [dbmate, migrations, gotcha, postgres]
---

# dbmate treats any line starting with -- migrate:up/down as a directive

dbmate splits a migration file into its up and down halves by scanning for **lines that begin with** `-- migrate:up` and `-- migrate:down`. The match is line-prefix based, not exact — so a **comment** you write inside the down section that happens to start with `-- migrate:up ...` is misread as a *second* up directive, corrupting the parse (dbmate may error or apply the wrong SQL half).

**Gotcha hit:** a no-op down section documented as `-- migrate:up recreates them idempotently` registered as a 2nd up marker. Fix: reword so no ordinary comment line starts with the literal `-- migrate:up` / `-- migrate:down` token (e.g. "The up section recreates ...").

**Rule:** in a dbmate .sql file, the only lines starting with `-- migrate:up` or `-- migrate:down` should be the real directives. Sanity check: `grep -cE '^-- migrate:(up|down)'` must return exactly 1 each. Same class of trap applies when wrapping a legacy SQL dump into a migration — scan the original body for stray `-- migrate:` prefixes too.

Related: [[leo-customer360 uses dbmate for Postgres migrations, not Alembic]].

## Related

- [[leo-customer360 uses dbmate for Postgres migrations]]
- [[not Alembic]]
