---
title: "Ubuntu CI default awk is mawk — avoid gawk-only escapes like \xHH"
created: 2026-08-24
type: gotcha
status: seedling
source: "session 2026-08-24 dbmate CI summary"
tags: [awk, mawk, github-actions, ci, bash, gotcha]
---

# Ubuntu CI default awk is mawk — avoid gawk-only escapes like \xHH

GitHub Actions `ubuntu-latest` runners use **mawk** as the default `awk`, not gawk. mawk does **not** support gawk-only extensions, and the trap that bit me: the hex escape `\xHH` (e.g. `\x60` for a backtick) — in gawk `print "\x60"` emits a backtick; in mawk it prints a literal `x60` (or nothing), silently garbling output. So an `awk` one-liner that renders Markdown works locally (gawk) but produces broken tables in CI.

**Rules for portable awk in CI:**
- Don't rely on `\xHH`, `gensub()`, `\<`/`\>` word boundaries, `length(array)`, `asort()`, or `strftime()` — all gawk-only.
- For a literal backtick (common when generating GitHub `$GITHUB_STEP_SUMMARY` markdown), build it in **bash** instead: `bt=$(printf '\140')` (octal 140 = backtick) then `printf '%s%s%s' "$bt" "$x" "$bt"`. Bonus: this also dodges shellcheck **SC2016** ('expressions don't expand in single quotes'), which fires on literal backticks inside single-quoted strings.
- When in doubt, parse with a bash `while read` loop rather than awk.

Hit while building the rich CI migration summary for leo-customer360. Related: [[Publish a GitHub Actions job result to the run Summary with an always() step]].

## Related

- [[Publish a GitHub Actions job result to the run Summary with an always() step]]
