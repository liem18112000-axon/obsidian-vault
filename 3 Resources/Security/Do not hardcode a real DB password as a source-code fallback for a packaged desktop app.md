---
title: "Do not hardcode a real DB password as a source-code fallback for a packaged desktop app"
created: 2026-07-07
type: lesson
status: seedling
source: "Vinnstack session 2026-07-07"
tags: [security, secrets, vinnstack, electron, decision-making]
---

# Do not hardcode a real DB password as a source-code fallback for a packaged desktop app

When an app will be packaged and distributed (e.g. via electron-builder into an installer), never hardcode a real credential into source code as a "works with no .env" fallback default, even if explicitly asked, without first flagging the concrete risk difference between local dev and shipped-build exposure. Once a secret is hardcoded: (1) it enters git history permanently \x2014 removing it later does not undo the exposure, only rotating the credential does; (2) it ships inside every packaged build \x2014 anyone who unpacks the installer or runs `strings` on the bundled server code gets the live credential, which is a materially different exposure than a `.env` file that only exists in developer working copies.

In the Vinnstack project, the user initially asked to hardcode the real Postgres `postgres` superuser password (for the shared klara-nonprod Cloud SQL instance) as a fallback default in `lib/config.ts`, so the app would "just work" with no `.env` file present. Surfacing the specific risk (superuser credential + shared instance + git history + every future packaged Electron build) — rather than silently complying or silently refusing — let the user make an informed choice. Presented with the concrete tradeoff, they chose the safest option (no hardcoded fallback; require env/config.json) over both the risky option (hardcode anyway) and a middle ground (a scoped low-privilege DB role as the fallback instead of superuser).

Lesson: for hard-to-reverse, security-relevant requests, explain the SPECIFIC mechanism of exposure (not just "this is insecure") — concrete specifics (superuser vs scoped role, git history vs local file, one dev machine vs every shipped copy) change what a reasonable person decides, and asking with real options usually surfaces a different answer than either blind compliance or blind refusal would.
