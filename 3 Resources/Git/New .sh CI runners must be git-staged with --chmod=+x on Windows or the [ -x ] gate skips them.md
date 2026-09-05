---
title: "New .sh CI runners must be git-staged with --chmod=+x on Windows or the [ -x ] gate skips them"
created: 2026-08-20
type: lesson
status: seedling
source: "session 2026-08-20"
tags: [git, windows, ci, leo-customer360, gotcha, shell]
---

# New .sh CI runners must be git-staged with --chmod=+x on Windows or the [ -x ] gate skips them

On Windows, a newly created shell script (e.g. a per-module test runner) is added to git with mode **100644** by default — the filesystem exec bit set by `chmod +x` is not recorded. Stage it as executable explicitly:

```bash
git add path/to/run_tests.sh
git update-index --chmod=+x path/to/run_tests.sh   # must be run AFTER the file is tracked
git ls-files -s path/to/run_tests.sh               # verify: expect 100755
```

**Why it matters:** `leo-customer360` CI (`.github/workflows/ci.yml`) delegates all testing to `run_all_tests.sh`, whose `run_suite` helper gates each suite behind `if [ -x "$runner" ]`. A runner committed as 100644 is not executable when checked out on the `ubuntu-latest` runner, so the suite is silently skipped and reported as failed ("Runner not found or not executable") — even though the script is perfectly valid.

**Companion gotcha — line endings:** the shebang must survive as LF on Linux. This repo has no `.gitattributes`, but `core.autocrlf` keeps the *stored blob* LF even while the Windows working copy shows CRLF (git warns "LF will be replaced by CRLF"). Verify the blob, not the working copy: `git show :path/to/run_tests.sh | od -c | head` — the shebang line should end in `\n` (`0a`), not `\r\n` (`0d 0a`). A CRLF shebang yields `/bin/bash^M: bad interpreter` on the runner.

**Repo convention:** each module (`customer360-api`, `ads-server`, `identity_resolution`, `segmentation`) carries its own self-contained runner that builds a `.venv`, `pip install`s `requirements.txt`, loads `.env`, and runs `pytest`. To add a suite to CI you wire its runner into `run_all_tests.sh`; you do not touch `ci.yml`.

## Related

- [[leo-customer360 CI]]
- [[core.autocrlf]]
