---
title: "Backgrounded shell exit code reflects the last command, not the build"
created: 2026-07-27
type: lesson
status: seedling
source: "session 2026-07-27 luz_finance setup"
tags: [shell, ci, gotcha, maven]
---

# Backgrounded shell exit code reflects the last command, not the build

When a shell command is launched in the background (or as a chained script) and the harness/CI reports an **exit code 0**, that code is the exit status of the **last command in the chain**, not necessarily the build. A wrapper like:

```bash
mvn clean install ...
echo "INSTALL_EXIT=$?"
```

exits 0 because the final `echo` succeeded — even if `mvn` returned 1 (BUILD FAILURE). **Always confirm success by reading the actual log** (look for `BUILD SUCCESS`/`BUILD FAILURE` and the captured `INSTALL_EXIT=`), never by trusting the process's top-level exit code alone. To make the script's exit reflect the real result, end with `exit $INSTALL_EXIT` or drop the trailing commands.
