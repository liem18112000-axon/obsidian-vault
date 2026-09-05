---
title: "Piping a command into tail masks its exit code without pipefail"
created: 2026-08-24
type: lesson
status: seedling
source: "session 2026-08-24 ecomart-java analysis"
tags: [bash, shell, pipefail, gotcha, ci]
---

# Piping a command into tail masks its exit code without pipefail

When you run `some_command | tail` (or `| grep`, `| head`, …) in a POSIX shell, the pipeline exit status is the **last** command's status, not the upstream one. So `./gradlew test 2>&1 | tail -80` reports exit code **0** even when Gradle prints `BUILD FAILED` — because `tail` succeeded.

This bites hard with background-task runners that report "completed (exit code 0)": the 0 is `tail`'s, and a real build/test failure hides behind it.

**Avoid the trap:**
- `set -o pipefail` — makes the pipeline return the first non-zero status in the chain.
- Or inspect the captured output text for `BUILD FAILED` / `FAILED` markers instead of trusting the code.
- Or drop the pipe and read the full output file directly.

General rule: never infer an upstream command's success from a piped exit code.

## Related

- [[Gradle toolchain languageVersion requires an exact JDK major version]]
