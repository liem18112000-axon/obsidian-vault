---
ai_hash: 54a18cffce34340b
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-19
entities: []
source: session 2026-06-19
status: seedling
tags:
- gcloud
- tts
- text-to-speech
- windows
- gotcha
title: 'Google Cloud TTS from Windows: fetch the token in bash, pass via env to Python'
type: lesson
---

# Google Cloud TTS from Windows: fetch the token in bash, pass via env to Python

When calling Google Cloud Text-to-Speech (the REST endpoint `texttospeech.googleapis.com/v1/text:synthesize`) from a Python script on Windows, do NOT try to shell out to gcloud from inside Python — `gcloud` is a `.cmd` batch wrapper and Python's subprocess can't launch it cleanly on Windows. Instead, fetch the access token in the **bash** layer and pass it into Python as an environment variable:

```bash
export GTOKEN=$(gcloud auth print-access-token)
LANG_CODE=VI python make-narrated-video.py
```

Then in Python read `os.environ['GTOKEN']` and send header `Authorization: Bearer $GTOKEN`. Two more required pieces: send `x-goog-user-project: <project>` (e.g. klara-nonprod) or the call is rejected, and use `audioConfig.audioEncoding = LINEAR16` to get a WAV you can measure duration from with the `wave` module. There is a ~5000-byte limit per request, so synthesize one chunk (e.g. per slide) at a time. The token is short-lived (~1h) — re-fetch per run.

%% ai-graph-start %%

**Related notes:**
- [[Vietnamese Google Cloud TTS write AI as trí tuệ nhân tạo, chunk per request, audio not video]]
- [[Assemble a narrated slide video pptx to png + per-slide Google TTS + ffmpeg -shortest segments + concat]]
- [[GCP APIs must be enabled individually per project]]
- [[Google Cloud TTS timepointing (enable_time_pointing) requires the v1beta1 client]]

%% ai-graph-end %%