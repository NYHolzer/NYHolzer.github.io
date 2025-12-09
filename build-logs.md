---
layout: page
title: "Build Logs"
permalink: /build-logs/
---

# 🔎 Build Logs

These are short write-ups on *how* I built certain projects — including how I use **ChatGPT as a pair programmer** to move faster while keeping control over design and logic.

Below are a few highlights. For the full list, see the posts below the intro.

---

## Featured Build Logs

### ⚙️ FinOps Platform
[Read the build log](./2025/10/01/finops-platform-build-log.html) – building a modular fintech operations platform with analyst tools, SEC/EDGAR data ingestion, metrics, reporting pipelines, and a clean Python package architecture.

### 🧮 FinOps Recon
[Read the build log](./2025/10/05/finops-recon-build-log.html) – building a practical reconciliation engine for banks, custodians, and internal ledgers, with ingestion, matching logic, exception handling, and a clean Python architecture.

### 🎙️ Local Transcriber
[Read the build log](./2025/12/08/local-transcriber-build-log.html) – an offline audio/video transcription app using Python, PySide6, FFmpeg, and faster-whisper with full editable UI and SRT export.

---

## All Build Logs

{% for post in site.posts %}
- [{{ post.title }}]({{ post.url }})
{% endfor %}
