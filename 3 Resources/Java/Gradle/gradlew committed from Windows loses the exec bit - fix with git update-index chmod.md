---
title: "gradlew committed from Windows loses the exec bit - fix with git update-index chmod"
created: 2026-06-07
type: lesson
status: seedling
source: "LEO CDP CI failure diagnosis, 2026-06-07"
tags: [git, gradle, windows, ci, docker, gotcha]
---

# gradlew committed from Windows loses the exec bit - fix with git update-index chmod

Committing a Gradle wrapper from Windows records `gradlew` as git mode **100644** (no exec bit - NTFS has none to preserve). Consequences surface only on Linux: CI checkouts produce a non-executable gradlew, and `RUN ./gradlew` in a Dockerfile fails with Permission denied (exit 126). Local Docker builds on the Windows box MASK the bug because buildx assigns 0755 to every file in a Windows build context. Fix: `git update-index --chmod=+x gradlew` + commit (works regardless of core.fileMode=false on Windows). Check with `git ls-files -s gradlew` - expect 100755.

## Related

- [[Check git check-ignore -v when adding a Gradle wrapper to a legacy repo]]
