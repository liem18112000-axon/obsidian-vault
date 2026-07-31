---
title: "TalkingHead speakAudio never decodes a single ArrayBuffer before playback"
created: 2026-07-11
type: lesson
status: seedling
source: "virtual-avatar session 2026-07-11, static/app.js + TalkingHead source read directly"
tags: [talkinghead, web-audio-api, decodeaudiodata, gotcha, javascript]
---

# TalkingHead speakAudio never decodes a single ArrayBuffer before playback

The `met4citizen/TalkingHead` JS lip-sync library's `speakAudio(r)` method documents its `Audio` typedef as accepting `audio: ArrayBuffer|ArrayBuffer[]` — but when you pass a single `ArrayBuffer` (e.g. raw MP3/WAV bytes from your own TTS backend, not a PCM-chunks array), the library never actually decodes it before playback. Confirmed by reading the source directly (both the `v1.7.0` tagged release and a later unreleased commit — same bug in both, so it's not a regression, it's a longstanding gap):

- `speakAudio()` does `o.audio = r.audio` and pushes it to the speech queue verbatim — no `decodeAudioData()` call.
- `playAudio()` then does: `if (Array.isArray(item.audio)) { audio = this.pcmToAudioBuffer(concatArrayBuffers(item.audio)) } else { audio = item.audio }` — the **array** branch converts raw PCM sample chunks into a real `AudioBuffer` via `pcmToAudioBuffer`, but the **non-array (single ArrayBuffer)** branch just uses the raw `ArrayBuffer` as-is.
- That raw `ArrayBuffer` then gets assigned directly to `AudioBufferSourceNode.buffer`, whose IDL type is `AudioBuffer?` — assigning a non-`AudioBuffer` value there is a type mismatch. The failure is silent from the user's perspective: no visible crash, page keeps working, but **no sound plays** (an unhandled promise rejection may appear in devtools console, easy to miss).
- Contrast: the library's *own* internal TTS path (`speakText()` calling Google TTS via `ttsEndpoint`) DOES properly call `await this.audioCtx.decodeAudioData(buf)` before pushing to the playlist — so that path works fine. Only the "bring your own pre-synthesized audio" path (`speakAudio()`) is missing this step.

**Fix, entirely on the caller's side** (no TalkingHead patch needed): decode the audio yourself before calling `speakAudio()`, using the **same `AudioContext` instance the `TalkingHead` object already owns** (`head.audioCtx`, a plain public instance property set synchronously in the constructor via `initAudioGraph()`) — an `AudioBuffer` decoded on a *different* `AudioContext` instance cannot be used with `AudioBufferSourceNode`s on this one, so you cannot create your own separate context:
```js
const audioBuffer = await head.audioCtx.decodeAudioData(rawArrayBuffer);
head.speakAudio({ audio: audioBuffer, words, wtimes, wdurations });
```
General lesson: when a library's documented parameter type is broader than what its internal handling actually supports, a silent playback/rendering failure (no error, just "nothing happens") is a strong signal to read the library's own source for the exact code path your input takes, rather than trusting the docstring/typedef alone.

## Related

- [[Unguarded top-level await in a module script blocks every statement after it]]
- [[jsdelivr gh CDN can pin to an exact commit SHA]]
- [[not just tags/branches]]
