# AI-Resume-Analyser
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>AI Resume Analyser</title>
<link href="https://fonts.googleapis.com/css2?family=DM+Serif+Display:ital@0;1&family=DM+Mono:wght@400;500&family=Syne:wght@400;500;600;700&display=swap" rel="stylesheet">
<style>
  *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }

  :root {
    --bg: #0d0d10;
    --surface: #16161c;
    --surface2: #1e1e27;
    --border: rgba(255,255,255,0.07);
    --border-glow: rgba(139,92,246,0.3);
    --text: #f0eeff;
    --text-muted: #7c7a8e;
    --text-dim: #4a4860;
    --accent: #7c3aed;
    --accent-light: #a78bfa;
    --accent-dim: rgba(124,58,237,0.12);
    --green: #10b981;
    --green-dim: rgba(16,185,129,0.12);
    --amber: #f59e0b;
    --amber-dim: rgba(245,158,11,0.12);
    --red: #ef4444;
    --red-dim: rgba(239,68,68,0.12);
    --cyan: #06b6d4;
    --radius: 12px;
    --radius-lg: 18px;
  }

  body {
    font-family: 'Syne', sans-serif;
    background: var(--bg);
    color: var(--text);
    min-height: 100vh;
    display: flex;
    flex-direction: column;
    align-items: center;
  }

  /* Grid background */
  body::before {
    content: '';
    position: fixed;
    inset: 0;
    background-image:
      linear-gradient(rgba(124,58,237,0.03) 1px, transparent 1px),
      linear-gradient(90deg, rgba(124,58,237,0.03) 1px, transparent 1px);
    background-size: 40px 40px;
    pointer-events: none;
    z-index: 0;
  }

  .container {
    width: 100%;
    max-width: 900px;
    padding: 2rem 1.5rem 4rem;
    position: relative;
    z-index: 1;
  }

  /* Header */
  header {
    text-align: center;
    margin-bottom: 3rem;
    padding-top: 2rem;
  }

  .badge {
    display: inline-flex;
    align-items: center;
    gap: 6px;
    background: var(--accent-dim);
    border: 1px solid var(--border-glow);
    border-radius: 999px;
    padding: 4px 14px;
    font-size: 11px;
    font-weight: 600;
    color: var(--accent-light);
    letter-spacing: 0.08em;
    text-transform: uppercase;
    margin-bottom: 1.25rem;
  }

  .badge::before {
    content: '';
    width: 6px; height: 6px;
    border-radius: 50%;
    background: var(--accent-light);
    box-shadow: 0 0 6px var(--accent-light);
    animation: pulse 2s ease-in-out infinite;
  }

  @keyframes pulse {
    0%, 100% { opacity: 1; }
    50% { opacity: 0.4; }
  }

  h1 {
    font-family: 'DM Serif Display', serif;
    font-size: clamp(2.2rem, 5vw, 3.5rem);
    font-weight: 400;
    line-height: 1.1;
    color: var(--text);
    margin-bottom: 0.75rem;
  }

  h1 em {
    font-style: italic;
    color: var(--accent-light);
  }

  .subtitle {
    color: var(--text-muted);
    font-size: 1rem;
    font-weight: 400;
    line-height: 1.6;
  }

  /* Upload zone */
  .upload-area {
    background: var(--surface);
    border: 1.5px dashed var(--border-glow);
    border-radius: var(--radius-lg);
    padding: 3rem 2rem;
    text-align: center;
    cursor: pointer;
    transition: all 0.2s ease;
    margin-bottom: 1.5rem;
    position: relative;
    overflow: hidden;
  }

  .upload-area::before {
    content: '';
    position: absolute;
    inset: 0;
    background: radial-gradient(ellipse at 50% 0%, rgba(124,58,237,0.08) 0%, transparent 60%);
    pointer-events: none;
  }

  .upload-area:hover, .upload-area.dragover {
    border-color: var(--accent-light);
    background: rgba(124,58,237,0.06);
  }

  .upload-icon {
    width: 52px; height: 52px;
    background: var(--accent-dim);
    border: 1px solid var(--border-glow);
    border-radius: 14px;
    display: flex; align-items: center; justify-content: center;
    margin: 0 auto 1rem;
    font-size: 24px;
  }

  .upload-title {
    font-size: 1.05rem;
    font-weight: 600;
    color: var(--text);
    margin-bottom: 0.35rem;
  }

  .upload-sub {
    font-size: 0.85rem;
    color: var(--text-muted);
  }

  input[type="file"] { display: none; }

  .file-selected {
    display: none;
    align-items: center;
    gap: 10px;
    background: var(--green-dim);
    border: 1px solid rgba(16,185,129,0.25);
    border-radius: 10px;
    padding: 10px 16px;
    margin-bottom: 1rem;
    font-size: 0.9rem;
    color: var(--green);
  }

  .file-selected.show { display: flex; }

  .file-selected span { flex: 1; white-space: nowrap; overflow: hidden; text-overflow: ellipsis; }

  .remove-file {
    background: none; border: none; color: var(--text-muted);
    cursor: pointer; font-size: 18px; line-height: 1; padding: 0;
  }
  .remove-file:hover { color: var(--red); }

  /* Textarea */
  .section-label {
    font-size: 0.75rem;
    font-weight: 600;
    letter-spacing: 0.08em;
    text-transform: uppercase;
    color: var(--text-muted);
    margin-bottom: 0.6rem;
    display: block;
  }

  textarea {
    width: 100%;
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: var(--radius);
    padding: 1rem 1.1rem;
    color: var(--text);
    font-family: 'Syne', sans-serif;
    font-size: 0.9rem;
    line-height: 1.6;
    resize: vertical;
    min-height: 110px;
    transition: border-color 0.2s;
    outline: none;
    margin-bottom: 1.25rem;
  }

  textarea:focus {
    border-color: var(--border-glow);
  }

  textarea::placeholder { color: var(--text-dim); }

  /* Focus areas */
  .focus-grid {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(140px, 1fr));
    gap: 8px;
    margin-bottom: 1.5rem;
  }

  .focus-chip {
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: 8px;
    padding: 8px 12px;
    font-size: 0.8rem;
    color: var(--text-muted);
    cursor: pointer;
    transition: all 0.15s;
    text-align: center;
    user-select: none;
  }

  .focus-chip:hover { border-color: var(--border-glow); color: var(--text); }
  .focus-chip.active {
    background: var(--accent-dim);
    border-color: var(--border-glow);
    color: var(--accent-light);
    font-weight: 600;
  }

  /* Analyse button */
  .btn-analyse {
    width: 100%;
    padding: 1rem;
    background: var(--accent);
    border: none;
    border-radius: var(--radius);
    color: #fff;
    font-family: 'Syne', sans-serif;
    font-size: 1rem;
    font-weight: 700;
    letter-spacing: 0.03em;
    cursor: pointer;
    transition: all 0.2s;
    position: relative;
    overflow: hidden;
  }

  .btn-analyse::before {
    content: '';
    position: absolute;
    inset: 0;
    background: linear-gradient(135deg, rgba(255,255,255,0.12) 0%, transparent 60%);
  }

  .btn-analyse:hover { background: #6d28d9; transform: translateY(-1px); }
  .btn-analyse:active { transform: translateY(0); }
  .btn-analyse:disabled { opacity: 0.4; cursor: not-allowed; transform: none; }

  /* Loading */
  .loading {
    display: none;
    flex-direction: column;
    align-items: center;
    gap: 1rem;
    padding: 3rem;
    text-align: center;
  }

  .loading.show { display: flex; }

  .loader-ring {
    width: 48px; height: 48px;
    border: 2px solid var(--border);
    border-top-color: var(--accent-light);
    border-radius: 50%;
    animation: spin 0.8s linear infinite;
  }

  @keyframes spin { to { transform: rotate(360deg); } }

  .loading-text {
    color: var(--text-muted);
    font-size: 0.9rem;
  }

  .loading-steps {
    font-family: 'DM Mono', monospace;
    font-size: 0.75rem;
    color: var(--text-dim);
    list-style: none;
    display: flex;
    flex-direction: column;
    gap: 4px;
    text-align: left;
  }

  .loading-steps li { opacity: 0.3; transition: opacity 0.4s; }
  .loading-steps li.active { opacity: 1; color: var(--accent-light); }
  .loading-steps li.done { opacity: 0.6; color: var(--green); }

  /* Results */
  .results { display: none; }
  .results.show { display: block; }

  .results-header {
    display: flex;
    align-items: center;
    justify-content: space-between;
    margin-bottom: 1.5rem;
    padding-bottom: 1rem;
    border-bottom: 1px solid var(--border);
  }

  .results-title {
    font-family: 'DM Serif Display', serif;
    font-size: 1.5rem;
    font-weight: 400;
  }

  .btn-restart {
    background: var(--surface2);
    border: 1px solid var(--border);
    border-radius: 8px;
    color: var(--text-muted);
    font-family: 'Syne', sans-serif;
    font-size: 0.8rem;
    font-weight: 600;
    padding: 6px 14px;
    cursor: pointer;
    transition: all 0.15s;
  }

  .btn-restart:hover { color: var(--text); border-color: var(--border-glow); }

  /* Score card */
  .score-hero {
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: var(--radius-lg);
    padding: 2rem;
    display: flex;
    align-items: center;
    gap: 2rem;
    margin-bottom: 1.5rem;
    position: relative;
    overflow: hidden;
  }

  .score-hero::before {
    content: '';
    position: absolute;
    top: -30px; right: -30px;
    width: 150px; height: 150px;
    background: radial-gradient(circle, rgba(124,58,237,0.15), transparent 70%);
    pointer-events: none;
  }

  .score-circle {
    flex-shrink: 0;
    position: relative;
  }

  .score-svg { transform: rotate(-90deg); }

  .score-track { fill: none; stroke: var(--surface2); stroke-width: 8; }
  .score-fill { fill: none; stroke-width: 8; stroke-linecap: round; transition: stroke-dashoffset 1.2s cubic-bezier(0.4,0,0.2,1); }

  .score-number {
    position: absolute;
    top: 50%; left: 50%;
    transform: translate(-50%, -50%);
    text-align: center;
  }

  .score-val {
    font-family: 'DM Serif Display', serif;
    font-size: 2.2rem;
    line-height: 1;
    color: var(--text);
  }

  .score-label { font-size: 0.7rem; color: var(--text-muted); text-transform: uppercase; letter-spacing: 0.06em; }

  .score-meta { flex: 1; }
  .score-grade {
    font-size: 0.75rem;
    font-weight: 700;
    letter-spacing: 0.1em;
    text-transform: uppercase;
    padding: 4px 12px;
    border-radius: 999px;
    display: inline-block;
    margin-bottom: 0.6rem;
  }

  .score-grade.excellent { background: var(--green-dim); color: var(--green); }
  .score-grade.good { background: var(--accent-dim); color: var(--accent-light); }
  .score-grade.average { background: var(--amber-dim); color: var(--amber); }
  .score-grade.poor { background: var(--red-dim); color: var(--red); }

  .score-desc { color: var(--text-muted); font-size: 0.9rem; line-height: 1.6; }

  /* Section cards */
  .sections-grid {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 1rem;
    margin-bottom: 1rem;
  }

  @media (max-width: 600px) { .sections-grid { grid-template-columns: 1fr; } }

  .card {
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: var(--radius);
    padding: 1.25rem;
  }

  .card-header {
    display: flex;
    align-items: center;
    gap: 8px;
    margin-bottom: 1rem;
    padding-bottom: 0.75rem;
    border-bottom: 1px solid var(--border);
  }

  .card-icon {
    width: 30px; height: 30px;
    border-radius: 8px;
    display: flex; align-items: center; justify-content: center;
    font-size: 15px;
    flex-shrink: 0;
  }

  .card-title { font-size: 0.85rem; font-weight: 700; color: var(--text); }

  .item-list { list-style: none; display: flex; flex-direction: column; gap: 8px; }

  .item {
    display: flex;
    gap: 10px;
    font-size: 0.82rem;
    color: var(--text-muted);
    line-height: 1.5;
  }

  .item-dot {
    width: 6px; height: 6px;
    border-radius: 50%;
    margin-top: 6px;
    flex-shrink: 0;
  }

  .dot-green { background: var(--green); }
  .dot-amber { background: var(--amber); }
  .dot-red { background: var(--red); }
  .dot-cyan { background: var(--cyan); }

  /* Full-width cards */
  .card-full {
    grid-column: 1 / -1;
  }

  /* Skill tags */
  .skill-tags { display: flex; flex-wrap: wrap; gap: 6px; margin-top: 0.25rem; }
  .tag {
    font-family: 'DM Mono', monospace;
    font-size: 0.72rem;
    padding: 3px 10px;
    border-radius: 999px;
    border: 1px solid;
  }

  .tag-present { background: var(--green-dim); color: var(--green); border-color: rgba(16,185,129,0.25); }
  .tag-missing { background: var(--red-dim); color: var(--red); border-color: rgba(239,68,68,0.25); }
  .tag-neutral { background: var(--accent-dim); color: var(--accent-light); border-color: var(--border-glow); }

  /* ATS bar */
  .ats-bars { display: flex; flex-direction: column; gap: 10px; }
  .ats-row { display: flex; flex-direction: column; gap: 4px; }
  .ats-label-row { display: flex; justify-content: space-between; font-size: 0.78rem; }
  .ats-label { color: var(--text-muted); }
  .ats-val { color: var(--text); font-weight: 600; }
  .ats-bar-bg { height: 5px; background: var(--surface2); border-radius: 999px; overflow: hidden; }
  .ats-bar-fill { height: 100%; border-radius: 999px; transition: width 1s ease; width: 0; }

  /* Error */
  .error-box {
    display: none;
    background: var(--red-dim);
    border: 1px solid rgba(239,68,68,0.25);
    border-radius: var(--radius);
    padding: 1rem 1.25rem;
    color: var(--red);
    font-size: 0.9rem;
    margin-bottom: 1rem;
  }
  .error-box.show { display: block; }

  .divider { height: 1px; background: var(--border); margin: 1rem 0; }
</style>
</head>
<body>
<div class="container">

  <header>
    <div class="badge">AI-Powered · Instant Insights</div>
    <h1>Resume <em>Analyser</em></h1>
    <p class="subtitle">Upload your resume and get a detailed AI analysis on skills, gaps, ATS compatibility, and improvement tips.</p>
  </header>

  <!-- Upload Form -->
  <div id="form-section">
    <div class="upload-area" id="drop-zone">
      <input type="file" id="file-input" accept=".pdf,.doc,.docx,.txt">
      <div class="upload-icon">📄</div>
      <div class="upload-title">Drop your resume here</div>
      <div class="upload-sub">PDF, Word, or plain text · Click to browse</div>
    </div>

    <div class="file-selected" id="file-info">
      <span>✅</span>
      <span id="file-name-display"></span>
      <button class="remove-file" id="remove-file" title="Remove file">×</button>
    </div>

    <label class="section-label">Job Description (optional)</label>
    <textarea id="job-desc" placeholder="Paste the job description here to get tailored gap analysis and keyword matching…"></textarea>

    <label class="section-label">Focus Areas</label>
    <div class="focus-grid">
      <div class="focus-chip active" data-focus="overall">Overall Review</div>
      <div class="focus-chip" data-focus="ats">ATS Compatibility</div>
      <div class="focus-chip" data-focus="skills">Skills Gap</div>
      <div class="focus-chip" data-focus="keywords">Keywords</div>
      <div class="focus-chip" data-focus="format">Formatting</div>
      <div class="focus-chip" data-focus="impact">Impact Statements</div>
    </div>

    <div class="error-box" id="error-box"></div>

    <button class="btn-analyse" id="analyse-btn" onclick="analyseResume()">
      ✦ Analyse My Resume
    </button>
  </div>

  <!-- Loading -->
  <div class="loading" id="loading-section">
    <div class="loader-ring"></div>
    <div class="loading-text">AI is reviewing your resume…</div>
    <ul class="loading-steps" id="loading-steps">
      <li id="step-1">📤 Extracting resume content</li>
      <li id="step-2">🔍 Analysing structure & format</li>
      <li id="step-3">⚡ Evaluating skills & keywords</li>
      <li id="step-4">📊 Generating score & recommendations</li>
    </ul>
  </div>

  <!-- Results -->
  <div class="results" id="results-section">
    <div class="results-header">
      <div class="results-title">Analysis Complete</div>
      <button class="btn-restart" onclick="restart()">← Start Over</button>
    </div>

    <!-- Score Hero -->
    <div class="score-hero" id="score-hero">
      <div class="score-circle">
        <svg class="score-svg" width="110" height="110" viewBox="0 0 110 110">
          <circle class="score-track" cx="55" cy="55" r="46"/>
          <circle class="score-fill" id="score-arc" cx="55" cy="55" r="46"
            stroke-dasharray="289" stroke-dashoffset="289" stroke="var(--accent-light)"/>
        </svg>
        <div class="score-number">
          <div class="score-val" id="score-val">0</div>
          <div class="score-label">/ 100</div>
        </div>
      </div>
      <div class="score-meta">
        <div class="score-grade" id="score-grade">Analysing…</div>
        <div class="score-desc" id="score-desc"></div>
      </div>
    </div>

    <!-- Cards grid -->
    <div class="sections-grid" id="cards-grid"></div>
  </div>

</div>

<script>
let selectedFile = null;
let selectedFocuses = new Set(['overall']);
let fileText = '';

const dropZone = document.getElementById('drop-zone');
const fileInput = document.getElementById('file-input');
const fileInfo = document.getElementById('file-info');
const fileNameDisplay = document.getElementById('file-name-display');

dropZone.addEventListener('click', () => fileInput.click());
fileInput.addEventListener('change', e => handleFile(e.target.files[0]));

dropZone.addEventListener('dragover', e => { e.preventDefault(); dropZone.classList.add('dragover'); });
dropZone.addEventListener('dragleave', () => dropZone.classList.remove('dragover'));
dropZone.addEventListener('drop', e => {
  e.preventDefault();
  dropZone.classList.remove('dragover');
  handleFile(e.dataTransfer.files[0]);
});

document.getElementById('remove-file').addEventListener('click', e => {
  e.stopPropagation();
  selectedFile = null; fileText = '';
  fileInfo.classList.remove('show');
  fileInput.value = '';
});

document.querySelectorAll('.focus-chip').forEach(chip => {
  chip.addEventListener('click', () => {
    chip.classList.toggle('active');
    const f = chip.dataset.focus;
    if (chip.classList.contains('active')) selectedFocuses.add(f);
    else selectedFocuses.delete(f);
  });
});

async function handleFile(file) {
  if (!file) return;
  selectedFile = file;
  fileNameDisplay.textContent = file.name;
  fileInfo.classList.add('show');

  // Read text from txt/simple files; for pdf/docx we send filename + ask AI to work with what it gets
  if (file.type === 'text/plain') {
    fileText = await file.text();
  } else {
    // For PDF/DOC, read as base64 to send to API
    fileText = await toBase64(file);
  }
}

function toBase64(file) {
  return new Promise((res, rej) => {
    const r = new FileReader();
    r.onload = () => res(r.result.split(',')[1]);
    r.onerror = rej;
    r.readAsDataURL(file);
  });
}

function setError(msg) {
  const eb = document.getElementById('error-box');
  eb.textContent = msg;
  eb.classList.add('show');
}

function clearError() {
  document.getElementById('error-box').classList.remove('show');
}

async function analyseResume() {
  clearError();

  if (!selectedFile && !document.getElementById('job-desc').value.trim()) {
    setError('Please upload a resume file or paste a job description to proceed.');
    return;
  }

  document.getElementById('form-section').style.display = 'none';
  document.getElementById('loading-section').classList.add('show');
  document.getElementById('results-section').classList.remove('show');

  animateLoadingSteps();

  const jobDesc = document.getElementById('job-desc').value.trim();
  const focuses = [...selectedFocuses].join(', ') || 'overall';

  // Build messages for API
  let messages = [];
  let systemPrompt = `You are an expert resume analyst and career coach. Analyse the provided resume and return ONLY a valid JSON object — no markdown fences, no explanation outside the JSON.

Return this exact structure:
{
  "score": <number 0-100>,
  "grade": "<Excellent|Good|Average|Poor>",
  "summary": "<2-3 sentence overall summary of the resume quality>",
  "strengths": ["<strength 1>", "<strength 2>", "<strength 3>"],
  "improvements": ["<improvement 1>", "<improvement 2>", "<improvement 3>"],
  "skills_found": ["<skill1>", "<skill2>"],
  "skills_missing": ["<skill1>", "<skill2>"],
  "keywords_matched": ["<kw1>", "<kw2>"],
  "keywords_missing": ["<kw1>", "<kw2>"],
  "ats": {
    "readability": <0-100>,
    "keyword_density": <0-100>,
    "format_score": <0-100>,
    "overall_ats": <0-100>
  },
  "quick_wins": ["<actionable tip 1>", "<actionable tip 2>", "<actionable tip 3>"]
}`;

  let userContent;

  if (selectedFile && (selectedFile.type === 'application/pdf')) {
    userContent = [
      {
        type: 'document',
        source: { type: 'base64', media_type: 'application/pdf', data: fileText }
      },
      {
        type: 'text',
        text: `Analyse this resume. Focus areas: ${focuses}.${jobDesc ? '\n\nJob Description:\n' + jobDesc : ''}`
      }
    ];
  } else {
    const resumeContent = fileText && selectedFile?.type === 'text/plain'
      ? `Resume Content:\n${fileText}`
      : selectedFile
        ? `Resume file: ${selectedFile.name} (extract insights from name/context if file unreadable)`
        : 'No resume file uploaded.';

    userContent = `${resumeContent}\n\nFocus areas: ${focuses}.${jobDesc ? '\n\nJob Description:\n' + jobDesc : ''}\n\nAnalyse this resume thoroughly.`;
  }

  messages.push({ role: 'user', content: userContent });

  try {
    const res = await fetch('https://api.anthropic.com/v1/messages', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({
        model: 'claude-sonnet-4-20250514',
        max_tokens: 1000,
        system: systemPrompt,
        messages
      })
    });

    const data = await res.json();

    if (data.error) throw new Error(data.error.message);

    const rawText = data.content.find(b => b.type === 'text')?.text || '';
    let analysis;
    try {
      analysis = JSON.parse(rawText.replace(/```json|```/g, '').trim());
    } catch {
      throw new Error('Could not parse AI response. Please try again.');
    }

    showResults(analysis);

  } catch (err) {
    document.getElementById('loading-section').classList.remove('show');
    document.getElementById('form-section').style.display = 'block';
    setError('Error: ' + (err.message || 'Something went wrong. Please try again.'));
  }
}

let stepTimer;
function animateLoadingSteps() {
  const steps = ['step-1', 'step-2', 'step-3', 'step-4'];
  let i = 0;
  steps.forEach(id => document.getElementById(id).className = '');
  stepTimer = setInterval(() => {
    if (i > 0) document.getElementById(steps[i - 1]).className = 'done';
    if (i < steps.length) {
      document.getElementById(steps[i]).className = 'active';
      i++;
    } else {
      clearInterval(stepTimer);
    }
  }, 900);
}

function showResults(a) {
  clearInterval(stepTimer);
  document.getElementById('loading-section').classList.remove('show');
  document.getElementById('results-section').classList.add('show');

  // Score
  setTimeout(() => {
    const score = Math.max(0, Math.min(100, a.score || 0));
    animateScore(score);
    document.getElementById('score-val').textContent = score;

    const grade = a.grade || 'Average';
    const gradeEl = document.getElementById('score-grade');
    gradeEl.textContent = grade;
    gradeEl.className = 'score-grade ' + grade.toLowerCase();

    document.getElementById('score-desc').textContent = a.summary || '';
  }, 100);

  // Build cards
  const grid = document.getElementById('cards-grid');
  grid.innerHTML = '';

  // Strengths
  if (a.strengths?.length) {
    grid.appendChild(makeListCard('💪 Strengths', a.strengths, 'dot-green',
      { icon: '💪', bg: 'var(--green-dim)' }));
  }

  // Improvements
  if (a.improvements?.length) {
    grid.appendChild(makeListCard('⚡ Areas to Improve', a.improvements, 'dot-amber',
      { icon: '⚡', bg: 'var(--amber-dim)' }));
  }

  // Skills
  if (a.skills_found?.length || a.skills_missing?.length) {
    const card = makeCard('🛠 Skills', '🛠', 'var(--accent-dim)');
    const body = card.querySelector('.card-body');
    if (a.skills_found?.length) {
      const label = document.createElement('div');
      label.style.cssText = 'font-size:0.7rem;color:var(--text-dim);text-transform:uppercase;letter-spacing:.06em;margin-bottom:6px;';
      label.textContent = 'Present';
      body.appendChild(label);
      const tags = document.createElement('div');
      tags.className = 'skill-tags';
      a.skills_found.slice(0, 10).forEach(s => {
        const t = document.createElement('span');
        t.className = 'tag tag-present';
        t.textContent = s;
        tags.appendChild(t);
      });
      body.appendChild(tags);
    }
    if (a.skills_missing?.length) {
      const label2 = document.createElement('div');
      label2.style.cssText = 'font-size:0.7rem;color:var(--text-dim);text-transform:uppercase;letter-spacing:.06em;margin:12px 0 6px;';
      label2.textContent = 'Missing / Suggested';
      body.appendChild(label2);
      const tags2 = document.createElement('div');
      tags2.className = 'skill-tags';
      a.skills_missing.slice(0, 10).forEach(s => {
        const t = document.createElement('span');
        t.className = 'tag tag-missing';
        t.textContent = s;
        tags2.appendChild(t);
      });
      body.appendChild(tags2);
    }
    grid.appendChild(card);
  }

  // ATS
  if (a.ats) {
    const card = makeCard('🤖 ATS Score', '🤖', 'rgba(6,182,212,0.12)');
    const body = card.querySelector('.card-body');
    const bars = document.createElement('div');
    bars.className = 'ats-bars';
    const metrics = [
      ['Readability', a.ats.readability, '#a78bfa'],
      ['Keyword Density', a.ats.keyword_density, '#10b981'],
      ['Format Score', a.ats.format_score, '#f59e0b'],
      ['ATS Overall', a.ats.overall_ats, '#06b6d4']
    ];
    metrics.forEach(([label, val, color]) => {
      val = Math.max(0, Math.min(100, val || 0));
      const row = document.createElement('div');
      row.className = 'ats-row';
      row.innerHTML = `<div class="ats-label-row"><span class="ats-label">${label}</span><span class="ats-val">${val}%</span></div>
        <div class="ats-bar-bg"><div class="ats-bar-fill" data-width="${val}" style="background:${color}"></div></div>`;
      bars.appendChild(row);
    });
    body.appendChild(bars);
    grid.appendChild(card);
    setTimeout(() => {
      grid.querySelectorAll('.ats-bar-fill').forEach(el => {
        el.style.width = el.dataset.width + '%';
      });
    }, 200);
  }

  // Keywords
  if (a.keywords_matched?.length || a.keywords_missing?.length) {
    const card = makeCard('🔑 Keywords', '🔑', 'var(--amber-dim)');
    const body = card.querySelector('.card-body');
    if (a.keywords_matched?.length) {
      const l = document.createElement('div');
      l.style.cssText = 'font-size:0.7rem;color:var(--text-dim);text-transform:uppercase;letter-spacing:.06em;margin-bottom:6px;';
      l.textContent = 'Matched';
      body.appendChild(l);
      const tags = document.createElement('div');
      tags.className = 'skill-tags';
      a.keywords_matched.slice(0, 8).forEach(s => {
        const t = document.createElement('span');
        t.className = 'tag tag-present';
        t.textContent = s;
        tags.appendChild(t);
      });
      body.appendChild(tags);
    }
    if (a.keywords_missing?.length) {
      const l2 = document.createElement('div');
      l2.style.cssText = 'font-size:0.7rem;color:var(--text-dim);text-transform:uppercase;letter-spacing:.06em;margin:10px 0 6px;';
      l2.textContent = 'Not Found';
      body.appendChild(l2);
      const tags2 = document.createElement('div');
      tags2.className = 'skill-tags';
      a.keywords_missing.slice(0, 8).forEach(s => {
        const t = document.createElement('span');
        t.className = 'tag tag-missing';
        t.textContent = s;
        tags2.appendChild(t);
      });
      body.appendChild(tags2);
    }
    grid.appendChild(card);
  }

  // Quick wins - full width
  if (a.quick_wins?.length) {
    const card = makeListCard('🚀 Quick Wins', a.quick_wins, 'dot-cyan',
      { icon: '🚀', bg: 'rgba(6,182,212,0.1)', fullWidth: true });
    grid.appendChild(card);
  }
}

function makeCard(title, icon, iconBg) {
  const card = document.createElement('div');
  card.className = 'card';
  card.innerHTML = `<div class="card-header">
    <div class="card-icon" style="background:${iconBg}">${icon}</div>
    <div class="card-title">${title}</div>
  </div>
  <div class="card-body"></div>`;
  return card;
}

function makeListCard(title, items, dotClass, opts = {}) {
  const card = document.createElement('div');
  card.className = 'card' + (opts.fullWidth ? ' card-full' : '');
  const ul = document.createElement('ul');
  ul.className = 'item-list';
  items.forEach(item => {
    const li = document.createElement('li');
    li.className = 'item';
    li.innerHTML = `<span class="item-dot ${dotClass}"></span><span>${item}</span>`;
    ul.appendChild(li);
  });
  card.innerHTML = `<div class="card-header">
    <div class="card-icon" style="background:${opts.bg || 'var(--accent-dim)'}">${opts.icon || '•'}</div>
    <div class="card-title">${title}</div>
  </div>`;
  card.appendChild(ul);
  return card;
}

function animateScore(score) {
  const arc = document.getElementById('score-arc');
  const circumference = 289;
  const offset = circumference - (score / 100) * circumference;
  const color = score >= 75 ? 'var(--green)' : score >= 50 ? 'var(--accent-light)' : score >= 30 ? 'var(--amber)' : 'var(--red)';
  arc.style.stroke = color;

  let current = 289;
  const target = offset;
  const step = (289 - target) / 60;
  let frame = 0;
  const timer = setInterval(() => {
    frame++;
    current = Math.max(target, current - step);
    arc.style.strokeDashoffset = current;
    const displayScore = Math.round(((289 - current) / 289) * score);
    document.getElementById('score-val').textContent = displayScore;
    if (current <= target || frame > 80) {
      clearInterval(timer);
      document.getElementById('score-val').textContent = score;
    }
  }, 16);
}

function restart() {
  selectedFile = null; fileText = '';
  document.getElementById('file-input').value = '';
  document.getElementById('file-info').classList.remove('show');
  document.getElementById('job-desc').value = '';
  document.getElementById('results-section').classList.remove('show');
  document.getElementById('form-section').style.display = 'block';
  clearError();
}
</script>
</body>
</html>
