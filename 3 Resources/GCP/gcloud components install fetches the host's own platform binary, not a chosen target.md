---
title: "gcloud components install fetches the host's own platform binary, not a chosen target"
created: 2026-07-07
type: lesson
status: seedling
source: "vinnstack cloudbuild.yaml setup, 2026-07-07"
tags: [gcp, gcloud, ci, cross-platform, gotcha]
---

# gcloud components install fetches the host's own platform binary, not a chosen target

`gcloud components install <component>` always installs the binary matching the **host machine gcloud is currently running on** — there'\''s no flag to ask for a different target OS/arch. Confirmed by running `gcloud components install cloud-sql-proxy` on a Windows machine and finding it installed `cloud-sql-proxy-windows-x86_64` (per the `.install/*.manifest`/`*.snapshot.json` files) with the binary landing at `google-cloud-sdk/bin/cloud-sql-proxy.exe`.

This matters for CI pipelines that cross-compile for a different OS than the CI runner'\''s own: e.g. a Linux Cloud Build step that needs to bundle a **Windows** binary into a Windows app can'\''t use `gcloud components install` for that binary, because running it inside the Linux container would fetch the Linux-native binary, not Windows. The fix is to download the specific platform asset directly by its known URL (e.g. from the tool'\''s GCS release bucket or GitHub releases), rather than going through the gcloud component installer.

## Related
[[Vinnstack bundles cloud-sql-proxy.exe as a gitignored extraResource]]
[[Cross-building Electron Windows exe on Linux needs wine]]
