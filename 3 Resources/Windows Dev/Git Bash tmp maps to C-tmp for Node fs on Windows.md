---
title: "Git Bash /tmp maps to C-tmp for Node fs on Windows"
created: 2026-08-26
type: lesson
status: seedling
source: "session 2026-08-26"
tags: [windows, git-bash, nodejs, gotcha, paths]
---

# Git Bash /tmp maps to C-tmp for Node fs on Windows

When a Node script is launched from **Git Bash on Windows**, a Unix-style path like `/tmp/foo` passed to Node's `fs` resolves to `C:\tmp\foo` — **not** the Git Bash temp dir the shell redirect (`> /tmp/foo`) actually wrote to. So `bash -c 'cmd > /tmp/x'` then `node -e 'fs.readFileSync("/tmp/x")'` fails with ENOENT because the two `/tmp`s are different places.

Fix: write and read temp files via an **absolute Windows path** (e.g. the session scratchpad dir) rather than `/tmp`, and pass that path into the Node script.

Root cause: the redirect is interpreted by MSYS/Git Bash (which has its own `/tmp` mount), while Node uses the Windows path resolver that maps a leading `/` to the current drive root.

Related: [[Generate Excalidraw triplet from one layout model, rasterize with @resvgresvg-js]]

## Related

- [[Generate Excalidraw triplet from one layout model]]
- [[rasterize with @resvgresvg-js]]
