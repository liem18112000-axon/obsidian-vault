---
title: "Since June 2023 code-signing certs must be on HSM — CI needs a cloud signing service, not a .pfx"
created: 2026-07-15
type: lesson
status: seedling
source: "session 2026-07-15 vinnstack signing research"
tags: [codesigning, windows, ci, authenticode, electron-builder, smartscreen]
---

# Since June 2023 code-signing certs must be on HSM — CI needs a cloud signing service, not a .pfx

As of **June 2023** (CA/Browser Forum baseline requirements), every newly issued code-signing certificate — both OV (Organization Validation) and EV (Extended Validation) — must have its private key stored on a **FIPS 140-2 Level 2+ hardware token or HSM**. CAs no longer issue exportable `.pfx`/`.p12` files.

**Consequence for CI/CD:** the classic automated-signing flow — drop a `.pfx` in the pipeline, set electron-builder’s `CSC_LINK` + `CSC_KEY_PASSWORD`, done — **is dead for any cert issued after mid-2023**. A USB hardware token can’t be plugged into a cloud CI runner. So automated signing now requires a **cloud signing service** that keeps the key in its HSM and signs over an API:

- **Azure Trusted Signing** — cheapest (~$10/mo), most CI-friendly; integrates with electron-builder via a custom `win.sign` / `signtoolOptions.sign` hook. OV-level SmartScreen reputation (builds over time).
- **DigiCert KeyLocker**, **SSL.com eSigner** — cloud HSM, OV or EV, pricier.
- An **EV cert on a physical token** gives INSTANT SmartScreen reputation but needs a dedicated signer host (not a cloud runner).

**Two separate benefits of signing** (don’t conflate): (1) a valid signature makes Windows Defender trust the binary and largely removes the unsigned-first-launch AV scan delay — any trusted-chain signature helps; (2) SmartScreen "protected your PC" suppression — only EV is instant, OV builds reputation.

## Related

- [[Unsigned asar:false Electron app: ~30s first-launch delay is Defender scanning loose files]]
