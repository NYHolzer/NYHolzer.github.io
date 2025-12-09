---
layout: page
title: "Projects"
permalink: /projects/
---

# 🛠️ Projects

I’m not an engineer — I’m an **operations leader who likes technology**  
and uses **AI-assisted coding** to build tools that solve real-world problems.

These projects represent how I think, how I build, and how I bring value through  
**fintech domain expertise + automation + practical problem solving**.

---

<style>
.project-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
  gap: 1.5rem;
  margin-top: 1.5rem;
}
.project-card {
  border: 1px solid #ddd;
  border-radius: 12px;
  padding: 1.25rem 1.4rem;
  background: #fafafa;
}
.project-card h2 {
  margin-top: 0;
  font-size: 1.3rem;
}
.project-card p {
  margin-bottom: 0.7rem;
}
.project-links a {
  margin-right: 0.75rem;
}
</style>

<div class="project-grid">

<!-- ========================= -->
<!-- FinOps Recon -->
<!-- ========================= -->

<div class="project-card">
<h2>🧮 FinOps Recon (Private)</h2>

<p><strong>What it is:</strong><br>
A reconciliation engine comparing transactions across <strong>banks</strong>, <strong>custodians</strong>, and <strong>internal ledgers</strong>. Designed for real fintech ops workflows.</p>

<p><strong>Highlights:</strong></p>
<ul>
  <li>Schema + data model for multi-source transaction ingestion</li>
  <li>Pandas-powered matching logic</li>
  <li>API-style architecture for recon jobs</li>
  <li>Designed with auditability and operational reality in mind</li>
</ul>

<p><strong>Why it matters:</strong><br>
Reconciliations are the heartbeat of fintech ops. This tool reflects knowledge of financial pipelines, exception workflows, and ledger consistency.</p>

<p><strong>Tech:</strong> Python · Pandas · SQLite · FastAPI-style design</p>

<div class="project-links">
  <a href="#">Repo: Private (available upon request)</a>
  <a href="/2025/10/05/finops-recon-build-log.html">Build Log</a>
</div>
</div>

<!-- ========================= -->
<!-- FinOps Platform -->
<!-- ========================= -->

<div class="project-card">
<h2>⚙️ FinOps Platform (Public)</h2>

<p>A public fintech automation lab exploring:</p>
<ul>
  <li>AI-driven analysis and workflows</li>
  <li>SEC/EDGAR data parsing</li>
  <li>Data enrichment and trade analysis</li>
  <li>Reconciliation extension modules</li>
</ul>

<p><strong>Why it matters:</strong><br>
Shows how I take operator-first thinking and translate it into modular, testable automation.</p>

<p><strong>Tech:</strong> Python · Modular Architecture · AI Patterns</p>

<div class="project-links">
  <a href="https://github.com/NYHolzer/finops-platform">GitHub Repo</a>
  <a href="/2025/10/01/finops-platform-build-log.html">Build Log</a>
</div>
</div>

<!-- ========================= -->
<!-- Torah Layout Studio -->
<!-- ========================= -->

<div class="project-card">
<h2>🕍 Torah Layout Studio (Public)</h2>

<p>A TypeScript/React experiment in structuring and rendering Torah text, commentaries, and translations — inspired by challenges creating:</p>

<ul>
  <li>Sefarim</li>
  <li>Divrei Torah</li>
  <li>Daf Yomi handouts</li>
  <li>Learning sheets</li>
</ul>

<p><strong>Highlights:</strong> Structured-text modeling, layout concepts, React component workflows.</p>

<p><strong>Tech:</strong> TypeScript · React</p>

<div class="project-links">
  <a href="https://github.com/NYHolzer/torah-layout-studio">GitHub Repo</a>
</div>
</div>

<!-- ========================= -->
<!-- Local Transcriber -->
<!-- ========================= -->

<div class="project-card">
<h2>🎙️ Local Audio/Video Transcriber</h2>

<p>A Python + PySide6 desktop app that transcribes audio/video <strong>fully offline</strong> using faster-whisper.</p>

<ul>
  <li>Edit segments inline</li>
  <li>Jump to timestamps</li>
  <li>Export .txt and .srt files</li>
</ul>

<p><strong>Tech:</strong> Python · PySide6 · FFmpeg · faster-whisper</p>

<div class="project-links">
  <a href="https://github.com/nyholzer/local-transcriber">GitHub Repo</a>
  <a href="/2025/12/08/local-transcriber-build-log.html">Build Log</a>
</div>
</div>

</div> <!-- END GRID -->

---

# ⚡ Additional Experiments & Utilities

These smaller tools show how I rapidly build useful utilities during real work:

### 🎙️ Local Audio Transcriber (Script Version)
Before the desktop app, a local script I used for transcribing shiurim and divrei Torah.

### 📰 PDF / Newspaper Splitter Tool
Utility for extracting sections from historical PDFs — built because research workflows are… messy.

### 📊 CSV Cleanup & Data Helpers
Little Python helpers for date normalization, column standardization, and export cleanup from banks/CRMs/custodians.

---

# 🧭 Build Philosophy

Across everything I build, my goals remain the same:

- **Reduce operational friction**  
- **Automate repetitive workflows**  
- **Create clarity in messy data**  
- **Use AI as a force multiplier**  
- **Ship fast and iterate intentionally**  

For deeper dives, check out my **[Build Logs](/build-logs/)**.
