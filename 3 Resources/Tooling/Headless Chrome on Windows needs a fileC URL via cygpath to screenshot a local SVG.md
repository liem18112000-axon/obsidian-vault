---
title: "Headless Chrome on Windows needs a file:///C:/ URL via cygpath to screenshot a local SVG"
created: 2026-08-19
type: lesson
status: seedling
source: "leo-customer360 deployments diagram, 2026-08"
tags: [chrome, headless, windows, svg, png, cygpath, gotcha]
---

# Headless Chrome on Windows needs a file:///C:/ URL via cygpath to screenshot a local SVG

To render a local SVG to PNG with headless Chrome on Windows from Git Bash, pass a real Windows file URL — `file:///C:/path/x.svg` built with `cygpath -m` — NOT the MSYS path `/c/path/x.svg`. If you feed the `/c/...` form (or `file:///$SVG` where $SVG is an MSYS path), Chrome loads its own ERR_FILE_NOT_FOUND error page and the screenshot captures THAT dark "Your file couldn't be accessed" page instead of your image — a silent failure that looks like a tiny/blank PNG.

Command that works:
`chrome --headless --disable-gpu --hide-scrollbars --force-device-scale-factor=2 --screenshot="$(cygpath -m OUT.png)" --window-size=W,H --default-background-color=FFFFFFFF "file:///$(cygpath -m IN.svg)"`

`cygpath -m` gives mixed form (`C:/...`, forward slashes) ideal for a URL; `--force-device-scale-factor=2` yields a crisp 2x raster; `--window-size` should match the SVG width/height. Always eyeball the resulting PNG (a ~40KB file that should be ~300KB is the tell it grabbed the error page).

Related: [[Generate a git patch under autocrlf so git apply matches a CRLF worktree]]

## Related

- [[Generate a git patch under autocrlf so git apply matches a CRLF worktree]]
