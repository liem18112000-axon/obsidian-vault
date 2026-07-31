---
title: "Offline English-Vietnamese MT for a Node-Electron app: Transformers.js + Xenova opus-mt-en-vi"
created: 2026-07-18
type: reference
status: seedling
source: "Vinnstack session 2026-07-18"
tags: [translation, offline, transformers-js, opus-mt, nllb, vinnstack, electron]
---

# Offline English-Vietnamese MT for a Node-Electron app: Transformers.js + Xenova opus-mt-en-vi

Research (2026-07) for a fast, free/offline English-to-Vietnamese translation path in the Vinnstack Electron/Next/Node app (to replace the ~12s claude CLI cold-start for the selection-assist translate action).

WINNER: Transformers.js (`@huggingface/transformers`, successor to `@xenova/transformers`) running `Xenova/opus-mt-en-vi` (ONNX).
- Confirmed: Xenova/opus-mt-en-vi exists on HF as an ONNX conversion of Helsinki-NLP/opus-mt-en-vi, compatible with Transformers.js. OPUS-MT/Marian en-vi is small (~78M params; quantized ONNX ~40-80MB).
- Runs IN-PROCESS in the Node backend (onnxruntime-node) -- load the pipeline ONCE (singleton), then translations are ~50-300ms. No per-request process spawn, so it kills the 12s floor. Fully offline after the model is cached/bundled (allowRemoteModels=false + localModelPath).
- Trade-off: OPUS-MT quality is "good gist", below an LLM/NLLB. Fine for a quick popup translation.

ALT: Xenova/nllb-200-distilled-600M -- better quality, 200 langs incl. vi, but 600M params so ONNX is hundreds of MB, slower load/infer. Overkill for a popup; use only if quality > size.

REJECTED: LibreTranslate + Argos Translate -- Vietnamese is in the ONLINE LibreTranslate but was REMOVED from the OFFLINE Argos models ~a year ago (unresolved). Also needs a separate server/Docker or Python runtime -- heavy for a desktop app.
REJECTED: Bergamot (Firefox WASM offline MT) -- no Vietnamese pair.

INTEGRATION GOTCHAS (Electron/Next): onnxruntime-node ships platform-specific native binaries (win32-x64) -- electron-builder must bundle them and Next must treat them as external (serverExternalPackages), not webpack-bundle them. Bundle the ~40-80MB model with the app for true offline + no first-run download. Verify model license (OPUS-MT usually CC-BY-4.0) before bundling.

Recommendation: OPUS-MT via Transformers.js for translate; keep the LLM path for "explain".

## Related

- [[Vinnstack per-request claude CLI spawn has a ~12s cold-start floor, model-independent]]
