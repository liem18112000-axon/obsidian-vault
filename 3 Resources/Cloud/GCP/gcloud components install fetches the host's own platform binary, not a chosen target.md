---
ai_hash: 152c7718921de2b1
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-07
entities: []
source: vinnstack cloudbuild.yaml setup, 2026-07-07
status: seedling
tags:
- gcp
- gcloud
- ci
- cross-platform
- gotcha
title: gcloud components install fetches the host's own platform binary, not a chosen
  target
type: lesson
---

# gcloud components install fetches the host's own platform binary, not a chosen target

`gcloud components install <component>` always installs the binary matching the **host machine gcloud is currently running on** — there'\''s no flag to ask for a different target OS/arch. Confirmed by running `gcloud components install cloud-sql-proxy` on a Windows machine and finding it installed `cloud-sql-proxy-windows-x86_64` (per the `.install/*.manifest`/`*.snapshot.json` files) with the binary landing at `google-cloud-sdk/bin/cloud-sql-proxy.exe`.

This matters for CI pipelines that cross-compile for a different OS than the CI runner'\''s own: e.g. a Linux Cloud Build step that needs to bundle a **Windows** binary into a Windows app can'\''t use `gcloud components install` for that binary, because running it inside the Linux container would fetch the Linux-native binary, not Windows. The fix is to download the specific platform asset directly by its known URL (e.g. from the tool'\''s GCS release bucket or GitHub releases), rather than going through the gcloud component installer.

## Related
[[Vinnstack bundles cloud-sql-proxy.exe as a gitignored extraResource]]
[[Cross-building Electron Windows exe on Linux needs wine]]

%% ai-graph-start %%

**Related notes:**
- [[Cross-building Electron Windows exe on Linux needs wine]]
- [[Vinnstack bundles cloud-sql-proxy.exe as a gitignored extraResource]]
- [[Dead bundling config outlives the runtime code that read it]]
- [[Cross-building an Electron+Next Windows exe on Linux omits the win32 SWC binary, so the packaged app fails at startup]]
- [[Publish a single file to GCS from Cloud Build with gcloud storage cp]]

%% ai-graph-end %%