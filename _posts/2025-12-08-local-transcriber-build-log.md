---
layout: post
title: "Local Transcriber: Offline Audio & Video Transcription App"
date: 2025-12-08
tags: [python, desktop, whisper, tools]
---

Over the past few days I built a small desktop app to transcribe my own audio and video files **fully offline** using [Whisper](https://github.com/openai/whisper) via [faster-whisper](https://github.com/SYSTRAN/faster-whisper).

You can find the code here:  
👉 [Local Transcriber on GitHub](https://github.com/nyholzer/local-transcriber)

---

## Why I built it

- I wanted to avoid transcription fees and API limits.  
- I didn’t want to upload private recordings to cloud services.  
- I needed a workflow where I can **listen + edit** in one place, then export `.txt` and `.srt`.

---

## Tech stack

- **Python** + **PySide6** for the desktop UI  
- **faster-whisper** for local ASR (Whisper models, CPU-friendly)  
- **FFmpeg** to:  
  - Read audio/video input  
  - Extract 16kHz mono WAV from video files for stable transcription  
- **Qt Multimedia** for playback (with a “Recover” button in case the audio backend stalls)

---

## Features

### ✔ Supports audio & video

- `.mp3 .wav .m4a .aac .flac .ogg .wma`  
- `.mp4 .mov .mkv .webm .avi`

### ✔ Smart video handling

When you open a video, the app:

- Extracts a 16kHz mono WAV to a temporary folder  
- Plays back the **original** video so timestamps match  
- Transcribes the WAV for optimal accuracy  

### ✔ Interactive editing UI

- Table of segments with:  
  **#**, **Start**, **End**, **Text**
- Click **#/Start/End** → jumps playback to that point  
- Double-click **Text** → inline editing  
- Export edited transcript as:  
  - **.txt** (one line per segment)  
  - **.srt** (subtitle format)

---

## Notes on Hebrew

- My recordings often mix English + Hebrew.  
- The app lets you choose model size *and* language.  
- For Hebrew, I use the multilingual Whisper models (`small`, `medium`, `large-v3`).  
- UI automatically right-aligns text rows that contain Hebrew characters.

---

## What I might add next

- Speaker diarization (open-source, no cloud APIs)  
- Embedded video player directly in the app window  
- Loop-a-segment mode for focused editing  
- Better waveform display for navigation  

---

If you'd like to try it or see how it works under the hood, here’s the repo again:

👉 **https://github.com/nyholzer/local-transcriber**

{% include build-log-footer.html %}