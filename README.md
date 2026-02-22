<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8"/>
<meta name="viewport" content="width=device-width, initial-scale=1.0"/>
<title>parth.patil ~ boot</title>
<link rel="preconnect" href="https://fonts.googleapis.com"/>
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin/>
<link href="https://fonts.googleapis.com/css2?family=JetBrains+Mono:ital,wght@0,100..800;1,100..800&family=Unbounded:wght@200;400;700;900&display=swap" rel="stylesheet"/>
<style>
  :root {
    --green: #00ff88;
    --green-dim: #00cc6a;
    --green-faint: rgba(0,255,136,0.06);
    --amber: #ffb347;
    --red: #ff4757;
    --blue: #00b4d8;
    --bg: #020c06;
    --bg2: #050f09;
    --card: rgba(0,255,136,0.04);
    --border: rgba(0,255,136,0.15);
    --text-dim: rgba(0,255,136,0.5);
    --text-bright: #e8fff3;
  }

  * { margin: 0; padding: 0; box-sizing: border-box; }

  html { font-size: 16px; }

  body {
    background: var(--bg);
    color: var(--green);
    font-family: 'JetBrains Mono', monospace;
    min-height: 100vh;
    overflow-x: hidden;
    cursor: default;
  }

  /* ── SCAN LINE OVERLAY ── */
  body::before {
    content: '';
    position: fixed;
    inset: 0;
    background: repeating-linear-gradient(
      to bottom,
      transparent,
      transparent 2px,
      rgba(0,0,0,0.07) 2px,
      rgba(0,0,0,0.07) 4px
    );
    pointer-events: none;
    z-index: 9999;
  }

  /* ── SCREEN FLICKER ── */
  @keyframes flicker {
    0%,100% { opacity: 1; }
    92% { opacity: 1; }
    93% { opacity: 0.8; }
    94% { opacity: 1; }
    96% { opacity: 0.9; }
    97% { opacity: 1; }
  }
  body { animation: flicker 8s infinite; }

  /* ── BOOT SCREEN ── */
  #boot {
    position: fixed;
    inset: 0;
    background: var(--bg);
    z-index: 1000;
    display: flex;
    flex-direction: column;
    padding: 40px 50px;
    transition: opacity 0.8s ease;
  }
  #boot.fade-out { opacity: 0; pointer-events: none; }

  .boot-line {
    opacity: 0;
    transform: translateY(4px);
    transition: opacity 0.1s ease, transform 0.1s ease;
    line-height: 1.8;
    font-size: 0.8rem;
    white-space: nowrap;
    overflow: hidden;
  }
  .boot-line.show { opacity: 1; transform: none; }

  .ok { color: var(--green); }
  .warn { color: var(--amber); }
  .err { color: var(--red); }
  .dim { color: var(--text-dim); }
  .bright { color: var(--text-bright); }
  .cyan { color: var(--blue); }

  .boot-progress {
    height: 2px;
    background: rgba(0,255,136,0.1);
    margin-top: 6px;
    border-radius: 99px;
    overflow: hidden;
  }
  .boot-progress-bar {
    height: 100%;
    background: var(--green);
    width: 0%;
    transition: width 0.05s linear;
    box-shadow: 0 0 8px var(--green);
  }

  #boot-ascii {
    font-size: clamp(0.35rem, 1.1vw, 0.62rem);
    color: var(--green);
    text-shadow: 0 0 8px var(--green);
    line-height: 1.15;
    margin-bottom: 24px;
    opacity: 0;
    transition: opacity 0.6s;
    letter-spacing: 0.02em;
  }
  #boot-ascii.show { opacity: 1; }

  /* ── MAIN PROFILE ── */
  #profile {
    opacity: 0;
    transition: opacity 1s ease;
    min-height: 100vh;
    padding: 0 0 80px;
  }
  #profile.show { opacity: 1; }

  /* ── NAV BAR ── */
  nav {
    display: flex;
    align-items: center;
    justify-content: space-between;
    padding: 16px 40px;
    border-bottom: 1px solid var(--border);
    background: rgba(0,0,0,0.5);
    backdrop-filter: blur(10px);
    position: sticky;
    top: 0;
    z-index: 100;
  }
  .nav-brand {
    font-family: 'Unbounded', sans-serif;
    font-size: 0.85rem;
    font-weight: 700;
    color: var(--green);
    text-shadow: 0 0 12px var(--green);
    letter-spacing: 0.06em;
  }
  .nav-links { display: flex; gap: 28px; }
  .nav-links a {
    color: var(--text-dim);
    text-decoration: none;
    font-size: 0.7rem;
    letter-spacing: 0.1em;
    text-transform: uppercase;
    transition: color 0.2s, text-shadow 0.2s;
  }
  .nav-links a:hover { color: var(--green); text-shadow: 0 0 8px var(--green); }

  .nav-status {
    display: flex;
    align-items: center;
    gap: 8px;
    font-size: 0.65rem;
    color: var(--text-dim);
  }
  .status-dot {
    width: 7px; height: 7px;
    border-radius: 50%;
    background: var(--green);
    box-shadow: 0 0 6px var(--green);
    animation: pulse-dot 2s infinite;
  }
  @keyframes pulse-dot {
    0%,100% { opacity: 1; }
    50% { opacity: 0.3; }
  }

  /* ── HERO ── */
  .hero {
    padding: 70px 60px 60px;
    display: grid;
    grid-template-columns: 1fr auto;
    gap: 40px;
    align-items: center;
    max-width: 1300px;
    margin: 0 auto;
    position: relative;
  }

  .hero::before {
    content: '';
    position: absolute;
    top: -20px; left: -100px;
    width: 500px; height: 500px;
    background: radial-gradient(circle, rgba(0,255,136,0.07) 0%, transparent 70%);
    pointer-events: none;
  }

  .hero-tag {
    display: inline-flex;
    align-items: center;
    gap: 8px;
    border: 1px solid var(--border);
    padding: 5px 14px;
    border-radius: 3px;
    font-size: 0.65rem;
    letter-spacing: 0.15em;
    text-transform: uppercase;
    color: var(--text-dim);
    margin-bottom: 20px;
    background: var(--green-faint);
  }

  .hero-name {
    font-family: 'Unbounded', sans-serif;
    font-size: clamp(2.5rem, 6vw, 5.2rem);
    font-weight: 900;
    line-height: 1;
    color: var(--text-bright);
    text-shadow: 0 0 40px rgba(0,255,136,0.2);
    margin-bottom: 6px;
  }
  .hero-name span { color: var(--green); text-shadow: 0 0 20px var(--green); }

  .hero-title {
    font-size: clamp(0.75rem, 1.5vw, 0.95rem);
    color: var(--text-dim);
    letter-spacing: 0.1em;
    text-transform: uppercase;
    margin-bottom: 28px;
  }
  .hero-title span { color: var(--amber); }

  .hero-desc {
    font-size: 0.85rem;
    color: rgba(255,255,255,0.5);
    line-height: 1.8;
    max-width: 520px;
    margin-bottom: 36px;
    border-left: 2px solid var(--border);
    padding-left: 16px;
  }

  .hero-btns { display: flex; gap: 14px; flex-wrap: wrap; }

  .btn-primary {
    background: var(--green);
    color: #000;
    border: none;
    padding: 11px 26px;
    font-family: 'JetBrains Mono', monospace;
    font-size: 0.75rem;
    font-weight: 700;
    letter-spacing: 0.08em;
    text-transform: uppercase;
    cursor: pointer;
    transition: all 0.2s;
    clip-path: polygon(0 0, calc(100% - 10px) 0, 100% 10px, 100% 100%, 0 100%);
    text-decoration: none;
    display: inline-block;
  }
  .btn-primary:hover {
    background: #00ff88cc;
    box-shadow: 0 0 20px rgba(0,255,136,0.5);
    transform: translateY(-1px);
  }

  .btn-ghost {
    background: transparent;
    color: var(--green);
    border: 1px solid var(--border);
    padding: 10px 26px;
    font-family: 'JetBrains Mono', monospace;
    font-size: 0.75rem;
    font-weight: 500;
    letter-spacing: 0.08em;
    text-transform: uppercase;
    cursor: pointer;
    transition: all 0.2s;
    text-decoration: none;
    display: inline-block;
  }
  .btn-ghost:hover {
    border-color: var(--green);
    background: var(--green-faint);
    box-shadow: 0 0 14px rgba(0,255,136,0.15);
  }

  /* ── TERMINAL CARD ── */
  .term-card {
    background: rgba(0,0,0,0.6);
    border: 1px solid var(--border);
    border-radius: 6px;
    overflow: hidden;
    min-width: 360px;
    box-shadow: 0 20px 60px rgba(0,0,0,0.6), 0 0 0 1px rgba(0,255,136,0.05);
  }
  .term-bar {
    background: rgba(0,255,136,0.06);
    padding: 9px 14px;
    display: flex;
    align-items: center;
    gap: 8px;
    border-bottom: 1px solid var(--border);
  }
  .t-dot { width: 11px; height: 11px; border-radius: 50%; }
  .t-red { background: #ff5f57; }
  .t-amber { background: #ffbd2e; }
  .t-green { background: #27c93f; }
  .term-filename { margin-left: auto; font-size: 0.65rem; color: var(--text-dim); }
  .term-body { padding: 18px 20px; line-height: 2.1; font-size: 0.78rem; }
  .k { color: #ff79c6; }
  .v { color: #f1fa8c; }
  .s { color: var(--green); }
  .p { color: var(--text-dim); }
  .arr { color: var(--blue); }

  /* ── SECTION LAYOUT ── */
  .section {
    max-width: 1300px;
    margin: 0 auto;
    padding: 50px 60px 0;
  }

  .section-header {
    display: flex;
    align-items: center;
    gap: 16px;
    margin-bottom: 32px;
  }
  .section-num {
    font-size: 0.6rem;
    color: var(--text-dim);
    letter-spacing: 0.1em;
  }
  .section-title {
    font-family: 'Unbounded', sans-serif;
    font-size: 0.85rem;
    font-weight: 700;
    letter-spacing: 0.12em;
    text-transform: uppercase;
    color: var(--text-bright);
  }
  .section-line {
    flex: 1;
    height: 1px;
    background: var(--border);
  }

  /* ── GRID CARDS ── */
  .cards-3 { display: grid; grid-template-columns: repeat(3,1fr); gap: 16px; }
  .cards-2 { display: grid; grid-template-columns: repeat(2,1fr); gap: 16px; }

  .card {
    border: 1px solid var(--border);
    background: var(--card);
    padding: 24px;
    position: relative;
    transition: border-color 0.2s, background 0.2s, transform 0.2s;
    overflow: hidden;
  }
  .card:hover {
    border-color: rgba(0,255,136,0.4);
    background: rgba(0,255,136,0.07);
    transform: translateY(-3px);
  }
  .card::before {
    content: attr(data-num);
    position: absolute;
    top: 16px; right: 18px;
    font-size: 0.6rem;
    color: rgba(0,255,136,0.2);
    letter-spacing: 0.1em;
    font-weight: 700;
  }
  .card-corner {
    position: absolute;
    bottom: 0; right: 0;
    width: 20px; height: 20px;
    border-top: 1px solid rgba(0,255,136,0.3);
    border-left: 1px solid rgba(0,255,136,0.3);
    transform: translate(10px, 10px) rotate(180deg);
  }

  .card-icon { font-size: 1.4rem; margin-bottom: 12px; display: block; }
  .card-label {
    font-size: 0.6rem;
    letter-spacing: 0.15em;
    text-transform: uppercase;
    color: var(--text-dim);
    margin-bottom: 6px;
  }
  .card-title {
    font-family: 'Unbounded', sans-serif;
    font-size: 0.85rem;
    font-weight: 700;
    color: var(--text-bright);
    margin-bottom: 10px;
    line-height: 1.3;
  }
  .card-desc {
    font-size: 0.72rem;
    color: rgba(255,255,255,0.4);
    line-height: 1.7;
  }

  /* ── SKILL BARS ── */
  .skills-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 16px 40px; }
  .skill-item { }
  .skill-top {
    display: flex;
    justify-content: space-between;
    align-items: baseline;
    margin-bottom: 6px;
  }
  .skill-name { font-size: 0.72rem; color: var(--text-bright); letter-spacing: 0.05em; }
  .skill-pct { font-size: 0.65rem; color: var(--text-dim); font-variant-numeric: tabular-nums; }
  .skill-track {
    height: 3px;
    background: rgba(0,255,136,0.1);
    position: relative;
    overflow: hidden;
  }
  .skill-fill {
    height: 100%;
    background: var(--green);
    box-shadow: 0 0 6px var(--green);
    transform: scaleX(0);
    transform-origin: left;
    transition: transform 1.2s cubic-bezier(0.16, 1, 0.3, 1);
  }
  .skill-fill.loaded { transform: scaleX(1); }

  /* ── PROJECTS ── */
  .proj-grid { display: grid; grid-template-columns: repeat(2, 1fr); gap: 20px; }

  .proj-card {
    border: 1px solid var(--border);
    padding: 28px;
    position: relative;
    background: var(--card);
    transition: all 0.25s;
    cursor: pointer;
    overflow: hidden;
  }
  .proj-card::after {
    content: '';
    position: absolute;
    inset: 0;
    background: radial-gradient(circle at var(--mx,50%) var(--my,50%), rgba(0,255,136,0.05) 0%, transparent 60%);
    opacity: 0;
    transition: opacity 0.3s;
  }
  .proj-card:hover::after { opacity: 1; }
  .proj-card:hover {
    border-color: rgba(0,255,136,0.35);
    transform: translateY(-4px);
    box-shadow: 0 16px 40px rgba(0,0,0,0.4);
  }
  .proj-card.featured {
    grid-column: span 2;
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 32px;
    align-items: center;
  }
  .proj-badge {
    display: inline-flex;
    align-items: center;
    gap: 6px;
    border: 1px solid var(--border);
    padding: 3px 10px;
    font-size: 0.58rem;
    letter-spacing: 0.15em;
    text-transform: uppercase;
    color: var(--text-dim);
    margin-bottom: 14px;
    background: var(--green-faint);
  }
  .proj-badge.active { border-color: var(--green); color: var(--green); }
  .proj-name {
    font-family: 'Unbounded', sans-serif;
    font-size: 1.1rem;
    font-weight: 700;
    color: var(--text-bright);
    margin-bottom: 10px;
  }
  .proj-desc { font-size: 0.75rem; color: rgba(255,255,255,0.45); line-height: 1.7; margin-bottom: 18px; }
  .proj-tags { display: flex; gap: 8px; flex-wrap: wrap; }
  .tag {
    font-size: 0.6rem;
    letter-spacing: 0.12em;
    text-transform: uppercase;
    padding: 3px 10px;
    border: 1px solid rgba(0,180,216,0.3);
    color: var(--blue);
    background: rgba(0,180,216,0.05);
  }
  .tag.green { border-color: rgba(0,255,136,0.3); color: var(--green); background: rgba(0,255,136,0.05); }
  .tag.amber { border-color: rgba(255,179,71,0.3); color: var(--amber); background: rgba(255,179,71,0.05); }

  .proj-visual {
    background: rgba(0,0,0,0.4);
    border: 1px solid rgba(0,255,136,0.08);
    height: 160px;
    display: flex;
    align-items: center;
    justify-content: center;
    position: relative;
    overflow: hidden;
  }
  .proj-visual-inner { font-size: 3.5rem; filter: drop-shadow(0 0 12px rgba(0,255,136,0.3)); }
  .proj-visual::before {
    content: '';
    position: absolute;
    inset: -50%;
    background: conic-gradient(from 0deg, transparent 0%, rgba(0,255,136,0.03) 30%, transparent 60%);
    animation: spin 15s linear infinite;
  }
  @keyframes spin { to { transform: rotate(360deg); } }

  .proj-links { display: flex; gap: 10px; margin-top: 20px; }
  .proj-link {
    font-size: 0.65rem;
    letter-spacing: 0.1em;
    text-transform: uppercase;
    color: var(--text-dim);
    text-decoration: none;
    border: 1px solid var(--border);
    padding: 6px 14px;
    transition: all 0.2s;
    display: inline-flex;
    align-items: center;
    gap: 6px;
  }
  .proj-link:hover { color: var(--green); border-color: var(--green); background: var(--green-faint); }
  .proj-link.primary { color: #000; background: var(--green); border-color: var(--green); }
  .proj-link.primary:hover { background: var(--green-dim); }

  /* ── STATS ROW ── */
  .stats-row {
    display: grid;
    grid-template-columns: repeat(4,1fr);
    gap: 1px;
    background: var(--border);
    border: 1px solid var(--border);
    margin-top: 16px;
  }
  .stat-cell {
    background: var(--bg);
    padding: 24px;
    text-align: center;
    transition: background 0.2s;
  }
  .stat-cell:hover { background: var(--card); }
  .stat-num {
    font-family: 'Unbounded', sans-serif;
    font-size: 1.8rem;
    font-weight: 700;
    color: var(--green);
    text-shadow: 0 0 16px var(--green);
    display: block;
  }
  .stat-label { font-size: 0.6rem; color: var(--text-dim); letter-spacing: 0.12em; text-transform: uppercase; }

  /* ── LANG BARS ── */
  .lang-bar-wrap { display: flex; flex-direction: column; gap: 12px; }
  .lang-bar-row { display: flex; align-items: center; gap: 12px; }
  .lang-bar-name { font-size: 0.72rem; color: var(--text-dim); width: 90px; flex-shrink: 0; }
  .lang-bar-track { flex: 1; height: 4px; background: rgba(0,255,136,0.08); overflow: hidden; }
  .lang-bar-fill {
    height: 100%;
    transition: width 1.5s cubic-bezier(0.16, 1, 0.3, 1);
    width: 0;
  }
  .lang-bar-fill.loaded { }
  .lang-bar-pct { font-size: 0.65rem; color: var(--text-dim); width: 36px; text-align: right; }

  /* ── CONNECT ── */
  .connect-grid { display: grid; grid-template-columns: repeat(3,1fr); gap: 14px; }
  .connect-card {
    border: 1px solid var(--border);
    padding: 20px;
    display: flex;
    align-items: center;
    gap: 14px;
    text-decoration: none;
    transition: all 0.2s;
    background: var(--card);
  }
  .connect-card:hover {
    border-color: var(--green);
    background: var(--green-faint);
    transform: translateX(4px);
  }
  .connect-icon { font-size: 1.5rem; }
  .connect-label { font-size: 0.6rem; color: var(--text-dim); letter-spacing: 0.1em; text-transform: uppercase; margin-bottom: 4px; }
  .connect-value { font-size: 0.78rem; color: var(--text-bright); }
  .connect-arrow { margin-left: auto; color: var(--text-dim); font-size: 1rem; transition: transform 0.2s; }
  .connect-card:hover .connect-arrow { transform: translateX(3px); color: var(--green); }

  /* ── FOOTER ── */
  footer {
    max-width: 1300px;
    margin: 60px auto 0;
    padding: 20px 60px;
    border-top: 1px solid var(--border);
    display: flex;
    justify-content: space-between;
    align-items: center;
  }
  .footer-copy { font-size: 0.62rem; color: var(--text-dim); }
  .footer-cmd { font-size: 0.62rem; color: rgba(0,255,136,0.25); }

  /* ── GLITCH ANIM ── */
  @keyframes glitch {
    0%,100% { text-shadow: 0 0 40px rgba(0,255,136,0.2); }
    20% { text-shadow: 2px 0 0 rgba(255,0,128,0.6), -2px 0 0 rgba(0,255,255,0.6); }
    40% { text-shadow: -2px 0 0 rgba(255,0,128,0.6), 2px 0 0 rgba(0,255,255,0.6); }
    60% { text-shadow: 0 0 40px rgba(0,255,136,0.2); }
  }
  .hero-name:hover { animation: glitch 0.3s ease; }

  /* ── TYPEWRITER ── */
  .typewriter {
    overflow: hidden;
    border-right: 2px solid var(--green);
    white-space: nowrap;
    animation: typing 2s steps(30) 0.5s forwards, blink-caret 0.8s step-end infinite;
    width: 0;
    max-width: 100%;
  }
  @keyframes typing { to { width: 100%; } }
  @keyframes blink-caret { 0%,100% { border-color: var(--green); } 50% { border-color: transparent; } }

  /* ── RESPONSIVE ── */
  @media (max-width: 900px) {
    .hero { grid-template-columns: 1fr; padding: 40px 24px; }
    .term-card { min-width: unset; }
    .section { padding: 40px 24px 0; }
    .cards-3, .cards-2, .proj-grid { grid-template-columns: 1fr; }
    .proj-card.featured { grid-column: span 1; grid-template-columns: 1fr; }
    .stats-row { grid-template-columns: repeat(2,1fr); }
    .connect-grid { grid-template-columns: 1fr; }
    .skills-grid { grid-template-columns: 1fr; }
    nav { padding: 12px 20px; }
    .nav-links { display: none; }
    footer { flex-direction: column; gap: 8px; text-align: center; padding: 20px 24px; }
  }
</style>
</head>
<body>

<!-- ══════════ BOOT SCREEN ══════════ -->
<div id="boot">
  <pre id="boot-ascii">
  ██████╗  █████╗ ██████╗ ████████╗██╗  ██╗    ██████╗  █████╗ ████████╗██╗██╗
  ██╔══██╗██╔══██╗██╔══██╗╚══██╔══╝██║  ██║    ██╔══██╗██╔══██╗╚══██╔══╝██║██║
  ██████╔╝███████║██████╔╝   ██║   ███████║    ██████╔╝███████║   ██║   ██║██║
  ██╔═══╝ ██╔══██║██╔══██╗   ██║   ██╔══██║    ██╔═══╝ ██╔══██║   ██║   ██║██║
  ██║     ██║  ██║██║  ██║   ██║   ██║  ██║    ██║     ██║  ██║   ██║   ██║███████╗
  ╚═╝     ╚═╝  ╚═╝╚═╝  ╚═╝   ╚═╝   ╚═╝  ╚═╝    ╚═╝     ╚═╝  ╚═╝   ╚═╝   ╚═╝╚══════╝
  </pre>

  <div id="boot-lines"></div>
  <div style="margin-top:10px;max-width:400px">
    <div class="boot-progress">
      <div class="boot-progress-bar" id="bpb"></div>
    </div>
  </div>
</div>

<!-- ══════════ MAIN PROFILE ══════════ -->
<div id="profile">

  <!-- NAV -->
  <nav>
    <div class="nav-brand">~/parth.patil</div>
    <div class="nav-links">
      <a href="#about">about</a>
      <a href="#projects">projects</a>
      <a href="#skills">skills</a>
      <a href="#contact">contact</a>
    </div>
    <div class="nav-status">
      <div class="status-dot"></div>
      <span>open to opportunities</span>
    </div>
  </nav>

  <!-- HERO -->
  <section class="hero" id="about">
    <div>
      <div class="hero-tag">
        <div class="status-dot" style="width:6px;height:6px;animation:none;background:var(--green);box-shadow:0 0 5px var(--green)"></div>
        cs student &nbsp;·&nbsp; india &nbsp;·&nbsp; 2025
      </div>
      <h1 class="hero-name">PARTH<br/><span>PATIL</span></h1>
      <p class="hero-title">Web Dev &nbsp;/&nbsp; <span>AI Builder</span> &nbsp;/&nbsp; CS Student</p>
      <p class="hero-desc">
        I build things for the web and explore AI systems. Currently deep in developing an intelligent email assistant that reads, summarizes, and drafts responses using LLMs. Turning caffeine into clean code since day one.
      </p>
      <div class="hero-btns">
        <a href="#projects" class="btn-primary">view_projects()</a>
        <a href="#contact" class="btn-ghost">ping_me →</a>
      </div>
    </div>

    <!-- TERMINAL CARD -->
    <div class="term-card">
      <div class="term-bar">
        <div class="t-dot t-red"></div>
        <div class="t-dot t-amber"></div>
        <div class="t-dot t-green"></div>
        <span class="term-filename">about-parth.ts</span>
      </div>
      <div class="term-body">
        <div><span class="k">const</span> <span class="s">parth</span> <span class="p">= {</span></div>
        <div>&nbsp;&nbsp;<span class="arr">🎓 education</span><span class="p">:</span> <span class="v">'B.Sc Computer Science'</span><span class="p">,</span></div>
        <div>&nbsp;&nbsp;<span class="arr">🚀 building</span><span class="p">:</span> <span class="v">'AI Email Assistant'</span><span class="p">,</span></div>
        <div>&nbsp;&nbsp;<span class="arr">💻 languages</span><span class="p">: [</span></div>
        <div>&nbsp;&nbsp;&nbsp;&nbsp;<span class="v">'TypeScript'</span><span class="p">,</span> <span class="v">'Python'</span><span class="p">,</span></div>
        <div>&nbsp;&nbsp;&nbsp;&nbsp;<span class="v">'JavaScript'</span><span class="p">,</span> <span class="v">'Java'</span></div>
        <div>&nbsp;&nbsp;<span class="p">],</span></div>
        <div>&nbsp;&nbsp;<span class="arr">🌐 frontend</span><span class="p">: [</span><span class="v">'React'</span><span class="p">,</span> <span class="v">'Angular'</span><span class="p">],</span></div>
        <div>&nbsp;&nbsp;<span class="arr">⚙️ backend</span><span class="p">: [</span><span class="v">'Flask'</span><span class="p">,</span> <span class="v">'Node.js'</span><span class="p">],</span></div>
        <div>&nbsp;&nbsp;<span class="arr">🛢️ db</span><span class="p">:</span> <span class="v">'MySQL'</span><span class="p">,</span></div>
        <div>&nbsp;&nbsp;<span class="arr">⚡ fun_fact</span><span class="p">:</span> <span class="v">'ships &gt; perfection'</span></div>
        <div><span class="p">}</span></div>
      </div>
    </div>
  </section>

  <!-- STATS -->
  <div style="max-width:1300px;margin:0 auto;padding:0 60px">
    <div class="stats-row">
      <div class="stat-cell">
        <span class="stat-num" data-count="4">0</span>
        <span class="stat-label">languages</span>
      </div>
      <div class="stat-cell">
        <span class="stat-num" data-count="5">0</span>
        <span class="stat-label">frameworks</span>
      </div>
      <div class="stat-cell">
        <span class="stat-num" data-count="1">0</span>
        <span class="stat-label">active projects</span>
      </div>
      <div class="stat-cell">
        <span class="stat-num">∞</span>
        <span class="stat-label">coffees consumed</span>
      </div>
    </div>
  </div>

  <!-- PROJECTS -->
  <section class="section" id="projects">
    <div class="section-header">
      <span class="section-num">01</span>
      <h2 class="section-title">Projects</h2>
      <div class="section-line"></div>
    </div>

    <div class="proj-grid">
      <!-- Featured: AI Email Assistant -->
      <div class="proj-card featured" id="proj1">
        <div>
          <div class="proj-badge active">
            <span style="width:6px;height:6px;background:var(--green);border-radius:50%;display:inline-block;box-shadow:0 0 5px var(--green)"></span>
            In Active Development
          </div>
          <h3 class="proj-name">AI Email Assistant</h3>
          <p class="proj-desc">
            An intelligent email assistant powered by large language models. Automatically reads your inbox, generates concise summaries, categorizes messages by priority, and drafts context-aware replies — all through a clean web interface.
          </p>
          <div class="proj-tags">
            <span class="tag green">Python</span>
            <span class="tag green">Flask</span>
            <span class="tag">React</span>
            <span class="tag">TypeScript</span>
            <span class="tag amber">LLM / AI</span>
            <span class="tag amber">Gmail API</span>
          </div>
          <div class="proj-links">
            <a href="#" class="proj-link">⌥ View Repo</a>
            <a href="#" class="proj-link primary">→ Live Preview</a>
          </div>
        </div>
        <div class="proj-visual">
          <div class="proj-visual-inner">📧</div>
        </div>
      </div>

      <!-- Placeholder Card 1 -->
      <div class="proj-card" id="proj2">
        <div class="proj-badge">Completed</div>
        <h3 class="proj-name" style="font-size:0.95rem">Web Projects</h3>
        <p class="proj-desc">Full-stack applications built with React, Angular on the front and Node.js / Flask on the back. MySQL-powered data persistence.</p>
        <div class="proj-tags" style="margin-top:auto">
          <span class="tag">React</span>
          <span class="tag">Node.js</span>
          <span class="tag green">MySQL</span>
        </div>
        <div class="proj-links">
          <a href="#" class="proj-link">⌥ View Repo</a>
        </div>
        <div class="card-corner"></div>
      </div>

      <!-- Placeholder Card 2 -->
      <div class="proj-card" id="proj3">
        <div class="proj-badge">Completed</div>
        <h3 class="proj-name" style="font-size:0.95rem">Python Utilities</h3>
        <p class="proj-desc">Scripts and automation tools built in Python. Data processing, API wrappers, and backend integrations using Flask and MySQL.</p>
        <div class="proj-tags" style="margin-top:auto">
          <span class="tag green">Python</span>
          <span class="tag">Flask</span>
          <span class="tag amber">Automation</span>
        </div>
        <div class="proj-links">
          <a href="#" class="proj-link">⌥ View Repo</a>
        </div>
        <div class="card-corner"></div>
      </div>
    </div>
  </section>

  <!-- SKILLS -->
  <section class="section" id="skills">
    <div class="section-header">
      <span class="section-num">02</span>
      <h2 class="section-title">Skills &amp; Stack</h2>
      <div class="section-line"></div>
    </div>

    <div class="cards-3" style="margin-bottom:28px">
      <div class="card" data-num="LANG">
        <span class="card-icon">⌨️</span>
        <div class="card-label">Languages</div>
        <div style="display:flex;flex-wrap:wrap;gap:8px;margin-top:10px">
          <span class="tag green">Java</span>
          <span class="tag green">Python</span>
          <span class="tag green">JavaScript</span>
          <span class="tag green">TypeScript</span>
        </div>
        <div class="card-corner"></div>
      </div>
      <div class="card" data-num="FE">
        <span class="card-icon">🌐</span>
        <div class="card-label">Frontend</div>
        <div style="display:flex;flex-wrap:wrap;gap:8px;margin-top:10px">
          <span class="tag">React</span>
          <span class="tag">Angular</span>
          <span class="tag">HTML5</span>
          <span class="tag">CSS3</span>
        </div>
        <div class="card-corner"></div>
      </div>
      <div class="card" data-num="BE">
        <span class="card-icon">⚙️</span>
        <div class="card-label">Backend &amp; DB</div>
        <div style="display:flex;flex-wrap:wrap;gap:8px;margin-top:10px">
          <span class="tag amber">Flask</span>
          <span class="tag amber">Node.js</span>
          <span class="tag">MySQL</span>
          <span class="tag">REST APIs</span>
        </div>
        <div class="card-corner"></div>
      </div>
    </div>

    <div style="display:grid;grid-template-columns:1fr 1fr;gap:32px;border:1px solid var(--border);padding:28px;background:var(--card)">
      <div>
        <div class="card-label" style="margin-bottom:18px">Proficiency</div>
        <div class="skills-grid" id="skill-bars">
          <div class="skill-item">
            <div class="skill-top"><span class="skill-name">Python</span><span class="skill-pct">85%</span></div>
            <div class="skill-track"><div class="skill-fill" style="width:85%"></div></div>
          </div>
          <div class="skill-item">
            <div class="skill-top"><span class="skill-name">JavaScript</span><span class="skill-pct">80%</span></div>
            <div class="skill-track"><div class="skill-fill" style="width:80%"></div></div>
          </div>
          <div class="skill-item">
            <div class="skill-top"><span class="skill-name">TypeScript</span><span class="skill-pct">75%</span></div>
            <div class="skill-track"><div class="skill-fill" style="width:75%"></div></div>
          </div>
          <div class="skill-item">
            <div class="skill-top"><span class="skill-name">React</span><span class="skill-pct">78%</span></div>
            <div class="skill-track"><div class="skill-fill" style="width:78%"></div></div>
          </div>
          <div class="skill-item">
            <div class="skill-top"><span class="skill-name">Java</span><span class="skill-pct">72%</span></div>
            <div class="skill-track"><div class="skill-fill" style="width:72%"></div></div>
          </div>
          <div class="skill-item">
            <div class="skill-top"><span class="skill-name">Flask / Node</span><span class="skill-pct">70%</span></div>
            <div class="skill-track"><div class="skill-fill" style="width:70%"></div></div>
          </div>
        </div>
      </div>
      <div>
        <div class="card-label" style="margin-bottom:18px">Language Mix</div>
        <div class="lang-bar-wrap">
          <div class="lang-bar-row">
            <div class="lang-bar-name">Python</div>
            <div class="lang-bar-track"><div class="lang-bar-fill" data-w="38%" style="background:var(--green);box-shadow:0 0 5px var(--green)"></div></div>
            <div class="lang-bar-pct">38%</div>
          </div>
          <div class="lang-bar-row">
            <div class="lang-bar-name">TypeScript</div>
            <div class="lang-bar-track"><div class="lang-bar-fill" data-w="28%" style="background:var(--blue);box-shadow:0 0 5px var(--blue)"></div></div>
            <div class="lang-bar-pct">28%</div>
          </div>
          <div class="lang-bar-row">
            <div class="lang-bar-name">JavaScript</div>
            <div class="lang-bar-track"><div class="lang-bar-fill" data-w="20%" style="background:var(--amber);box-shadow:0 0 5px var(--amber)"></div></div>
            <div class="lang-bar-pct">20%</div>
          </div>
          <div class="lang-bar-row">
            <div class="lang-bar-name">Java</div>
            <div class="lang-bar-track"><div class="lang-bar-fill" data-w="14%" style="background:#ff6b81;box-shadow:0 0 5px #ff6b81"></div></div>
            <div class="lang-bar-pct">14%</div>
          </div>
        </div>

        <div style="margin-top:28px">
          <div class="card-label" style="margin-bottom:14px">Tools &amp; Workflow</div>
          <div style="display:flex;flex-wrap:wrap;gap:8px">
            <span class="tag">Git</span>
            <span class="tag">GitHub</span>
            <span class="tag">VS Code</span>
            <span class="tag green">AI / LLM APIs</span>
            <span class="tag amber">Postman</span>
          </div>
        </div>
      </div>
    </div>
  </section>

  <!-- CONNECT -->
  <section class="section" id="contact">
    <div class="section-header">
      <span class="section-num">03</span>
      <h2 class="section-title">Connect</h2>
      <div class="section-line"></div>
    </div>
    <div class="connect-grid">
      <a href="https://linkedin.com/in/your-link" target="_blank" class="connect-card">
        <span class="connect-icon">💼</span>
        <div>
          <div class="connect-label">LinkedIn</div>
          <div class="connect-value">linkedin.com/in/your-link</div>
        </div>
        <span class="connect-arrow">→</span>
      </a>
      <a href="mailto:your-email@gmail.com" class="connect-card">
        <span class="connect-icon">📬</span>
        <div>
          <div class="connect-label">Email</div>
          <div class="connect-value">your-email@gmail.com</div>
        </div>
        <span class="connect-arrow">→</span>
      </a>
      <a href="https://github.com/YOUR_USERNAME" target="_blank" class="connect-card">
        <span class="connect-icon">🐙</span>
        <div>
          <div class="connect-label">GitHub</div>
          <div class="connect-value">github.com/YOUR_USERNAME</div>
        </div>
        <span class="connect-arrow">→</span>
      </a>
    </div>
  </section>

  <!-- FOOTER -->
  <footer>
    <div class="footer-copy">© 2025 Parth Patil &nbsp;·&nbsp; Built with &lt;curiosity /&gt;</div>
    <div class="footer-cmd">$ echo "let's build something cool"</div>
  </footer>

</div>

<script>
// ═══ BOOT SEQUENCE ═══
const bootMessages = [
  { text: "BIOS v2.4.1 — PARTH_OS Kernel loading...", cls: "dim", delay: 0 },
  { text: "[ OK ] Initializing core modules", cls: "ok", delay: 120 },
  { text: "[ OK ] Mounting /dev/brain ........... success", cls: "ok", delay: 180 },
  { text: "[ OK ] Loading Python runtime ........ 3.12.0", cls: "ok", delay: 240 },
  { text: "[ OK ] Loading Node.js runtime ........ 20.x LTS", cls: "ok", delay: 300 },
  { text: "[ -- ] Fetching coffee supply ......... please wait", cls: "warn", delay: 380 },
  { text: "[ OK ] Coffee supply: FULL ☕ ........ ready", cls: "ok", delay: 520 },
  { text: "[ OK ] Loading Stack: React, Angular, Flask, Node.js", cls: "ok", delay: 600 },
  { text: "[ OK ] Database drivers: MySQL connected", cls: "ok", delay: 680 },
  { text: "[ -- ] Connecting AI Email Assistant ... building", cls: "warn", delay: 760 },
  { text: "[ OK ] Git hooks: enabled", cls: "ok", delay: 860 },
  { text: "[ OK ] VS Code: IDE of choice detected", cls: "ok", delay: 940 },
  { text: "", cls: "", delay: 1000 },
  { text: "  Welcome, stranger. Parth Patil's dev portfolio is loading.", cls: "bright", delay: 1060 },
  { text: "", cls: "", delay: 1100 },
];

const container = document.getElementById('boot-lines');
const ascii = document.getElementById('boot-ascii');
const bpb = document.getElementById('bpb');
const boot = document.getElementById('boot');
const profile = document.getElementById('profile');

setTimeout(() => ascii.classList.add('show'), 50);

let maxDelay = 0;
bootMessages.forEach(msg => {
  if (msg.delay > maxDelay) maxDelay = msg.delay;
  setTimeout(() => {
    const line = document.createElement('div');
    line.className = `boot-line ${msg.cls}`;
    line.textContent = msg.text || '\u00a0';
    container.appendChild(line);
    requestAnimationFrame(() => { line.offsetHeight; line.classList.add('show'); });

    const prog = (msg.delay / (maxDelay + 400)) * 100;
    bpb.style.width = prog + '%';
  }, msg.delay + 300);
});

const totalBootTime = maxDelay + 1200;
setTimeout(() => {
  bpb.style.width = '100%';
  setTimeout(() => {
    boot.classList.add('fade-out');
    profile.style.opacity = '0';
    profile.classList.add('show');
    setTimeout(() => { profile.style.opacity = '1'; }, 100);
    boot.addEventListener('transitionend', () => boot.style.display = 'none', { once: true });
    // Trigger animations
    setTimeout(triggerAnimations, 400);
  }, 300);
}, totalBootTime);

function triggerAnimations() {
  // Skill bars
  document.querySelectorAll('.skill-fill').forEach(el => { el.classList.add('loaded'); });

  // Lang bars
  document.querySelectorAll('.lang-bar-fill').forEach(el => {
    el.style.width = el.dataset.w;
  });

  // Count-up
  document.querySelectorAll('[data-count]').forEach(el => {
    const target = parseInt(el.dataset.count);
    let cur = 0;
    const interval = setInterval(() => {
      cur++;
      el.textContent = cur;
      if (cur >= target) clearInterval(interval);
    }, 120);
  });
}

// Project card mouse follow
document.querySelectorAll('.proj-card').forEach(card => {
  card.addEventListener('mousemove', e => {
    const rect = card.getBoundingClientRect();
    card.style.setProperty('--mx', ((e.clientX - rect.left) / rect.width * 100) + '%');
    card.style.setProperty('--my', ((e.clientY - rect.top) / rect.height * 100) + '%');
  });
});
</script>
</body>
</html>
