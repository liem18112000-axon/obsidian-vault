---
title: "GitHub Release assets are capped at 2 GiB per file"
created: 2026-06-17
type: lesson
status: seedling
source: "session 2026-06-17"
tags: [github, release, packaging, gotcha]
---

# GitHub Release assets are capped at 2 GiB per file

GitHub limits each **Release asset to 2 GiB per file**; uploading a larger file fails with `HTTP 422 Validation Failed — size must be less than 2147483648`.

To distribute something bigger than 2 GiB through a Release, split it into sub-2 GiB volumes and attach every part as a separate asset. With 7-Zip:

```
7z a -mx=1 -v1900m bundle.7z <folder>   # -> bundle.7z.001, .002, ...
```

Also attach a standalone `7zr.exe` (from 7-zip.org/a/7zr.exe) so an **air-gapped** recipient can reassemble without installing anything: `7zr.exe x bundle.7z.001` rebuilds the folder from all parts in the same directory.

**Why:** the only native-to-the-platform alternatives (single zip) hit the cap; split volumes are the standard workaround for large GitHub Release downloads.

## Related

- [[GitHub Actions artifacts need login to download; Release assets do not]]
