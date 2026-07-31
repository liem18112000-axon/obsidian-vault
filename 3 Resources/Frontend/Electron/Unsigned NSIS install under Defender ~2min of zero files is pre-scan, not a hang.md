---
title: "Unsigned NSIS install under Defender: ~2min of zero files is pre-scan, not a hang"
created: 2026-07-17
type: lesson
status: seedling
source: "Vinnstack session 2026-07-17"
tags: [electron-builder, nsis, windows-defender, install, gotcha, vinnstack, code-signing]
---

# Unsigned NSIS install under Defender: ~2min of zero files is pre-scan, not a hang

Installing a large **unsigned** NSIS oneClick Electron app (~160MB installer, ~20k files unpacked with asar:false) on a machine with Windows Defender real-time ON can take **~5–6 minutes**, and — critically — the install dir shows **0 files for the first ~2 minutes**. That initial dead period is Defender scanning the entire unsigned payload *before* NSIS begins extraction; it looks hung but is not. Measured timeline on one run: 0 files until ~107s → Vinnstack.exe appears at 117s → files climb 562→19,853 over the next ~3 min (each file scanned as written) → stable/complete at ~325s.

**Consequence / trap:** giving up after 90–180s (a reasonable-seeming timeout) aborts right as extraction is about to start, making it look like the installer is permanently stuck at 0 files. It isn't — it needs patience. The installer process is CPU/IO-busy the whole time (Read/Write counters climb), not deadlocked; confirm 'working vs blocked' by sampling its Win32_Process ReadTransferCount/WriteTransferCount rather than only the install-dir file count.

**No-reboot / no-security-change fix:** just wait ~6 min. Faster options (need admin/consent, reversible): a Defender path exclusion for %TEMP% + the install dir, or pausing real-time protection during install. **Permanent fix:** code-sign the binary (signed → Defender scans leniently → install returns to ~3s). Same Defender-vs-unsigned-binary root cause as the app's first-launch require() stall.

Context: Vinnstack, electron-builder NSIS oneClick (perMachine:false), Electron 32, unsigned.

## Related

- [[Unsigned Electron app first-launch: transient Cannot find module during Defender post-install scan]]
