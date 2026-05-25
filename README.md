
 <!DOCTYPE html>
<html lang="es">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Ferretería Digital · Subasta Inversa · Sistema Integral</title>
<style>
  @import url('https://fonts.googleapis.com/css2?family=Bebas+Neue&family=DM+Sans:wght@300;400;500;600&display=swap');

  *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }

  :root {
    --amber:   #F5A623;
    --amber-d: #C27A0A;
    --amber-l: #FFD580;
    --bg:      #0D0E12;
    --surface: #161820;
    --surface2:#1E2028;
    --surface3:#282C38;
    --line:    rgba(255,255,255,0.08);
    --text:    #F0F0F0;
    --muted:   #8A8FA5;
    --red:     #E05252;
    --green:   #4CAF7E;
    --blue:    #4F9FE8;
    --slide-w: 100vw;
    --slide-h: 100vh;
  }

  html, body {
    width: 100%; height: 100%; overflow: hidden;
    background: var(--bg); color: var(--text);
    font-family: 'DM Sans', system-ui, sans-serif;
  }

  /* ── SCENE ── */
  #scene {
    width: 100vw; height: 100vh;
    perspective: 1200px;
    perspective-origin: 50% 50%;
    overflow: hidden;
  }

  #track {
    width: 100%; height: 100%;
    position: relative;
    transform-style: preserve-3d;
    transition: transform 0.8s cubic-bezier(0.77,0,0.175,1);
  }

  /* ── SLIDES ── */
  .slide {
    position: absolute; inset: 0;
    display: flex; align-items: center; justify-content: center;
    backface-visibility: hidden;
    opacity: 0; pointer-events: none;
    transition: opacity 0.4s;
  }
  .slide.active { opacity: 1; pointer-events: auto; }

  /* ── NAV ── */
  #nav {
    position: fixed; bottom: 2rem; left: 50%; transform: translateX(-50%);
    display: flex; gap: 0.75rem; align-items: center; z-index: 100;
    background: rgba(13,14,18,0.85); backdrop-filter: blur(10px);
    border: 1px solid var(--line); border-radius: 50px;
    padding: 0.5rem 1.25rem;
  }
  #nav button {
    background: none; border: none; color: var(--muted);
    font-size: 1.4rem; cursor: pointer; padding: 0.2rem 0.5rem;
    border-radius: 8px; transition: color 0.2s, background 0.2s;
  }
  #nav button:hover { color: var(--amber); background: rgba(245,166,35,0.1); }
  #counter { font-size: 0.8rem; color: var(--muted); min-width: 48px; text-align: center; }

  #dots {
    position: fixed; right: 1.5rem; top: 50%; transform: translateY(-50%);
    display: flex; flex-direction: column; gap: 8px; z-index: 100;
  }
  .dot {
    width: 8px; height: 8px; border-radius: 50%;
    background: var(--line); border: 1px solid var(--muted);
    cursor: pointer; transition: background 0.3s, transform 0.3s;
  }
  .dot.active { background: var(--amber); transform: scale(1.4); border-color: var(--amber); }

  /* ── PROGRESS ── */
  #prog-bar {
    position: fixed; top: 0; left: 0; height: 3px;
    background: linear-gradient(90deg, var(--amber-d), var(--amber));
    z-index: 200; transition: width 0.5s ease;
  }

  /* ── LOGO BADGE ── */
  #badge {
    position: fixed; top: 1.25rem; left: 1.5rem; z-index: 100;
    display: flex; align-items: center; gap: 0.6rem;
    font-size: 0.7rem; font-weight: 500; color: var(--muted);
    letter-spacing: 0.08em; text-transform: uppercase;
  }
  #badge span { color: var(--amber); }

  /* ═══════════════════════════════ SLIDE LAYOUTS ══════════════════════════════ */

  /* ─ COVER ─ */
  .cover-inner {
    max-width: 900px; padding: 4rem; text-align: center;
    position: relative;
  }
  .cover-eyebrow {
    font-size: 0.75rem; letter-spacing: 0.2em; text-transform: uppercase;
    color: var(--amber); margin-bottom: 1.5rem;
  }
  .cover-title {
    font-family: 'Bebas Neue', sans-serif;
    font-size: clamp(3.5rem, 8vw, 7rem);
    line-height: 0.95; color: #fff;
    text-shadow: 0 0 80px rgba(245,166,35,0.3);
    margin-bottom: 0.5rem;
  }
  .cover-title em { color: var(--amber); font-style: normal; }
  .cover-sub {
    font-size: 1.05rem; color: var(--muted); max-width: 560px;
    margin: 1.5rem auto 0; line-height: 1.7;
  }
  .cover-version {
    display: inline-flex; gap: 1.5rem; margin-top: 2.5rem;
    border: 1px solid var(--line); border-radius: 50px;
    padding: 0.5rem 1.5rem; font-size: 0.78rem; color: var(--muted);
  }
  .cover-version strong { color: var(--amber); }

  /* 3D floating card behind cover */
  .cube-bg {
    position: absolute; top: 50%; left: 50%;
    transform: translate(-50%,-50%);
    width: 500px; height: 500px;
    pointer-events: none; z-index: -1;
    animation: slow-rot 20s linear infinite;
  }
  @keyframes slow-rot {
    from { transform: translate(-50%,-50%) rotateX(20deg) rotateY(0deg); }
    to   { transform: translate(-50%,-50%) rotateX(20deg) rotateY(360deg); }
  }
  .cube-ring {
    position: absolute; inset: 0;
    border: 1px solid rgba(245,166,35,0.15);
    border-radius: 50%;
  }
  .cube-ring:nth-child(2) { transform: rotateX(60deg); }
  .cube-ring:nth-child(3) { transform: rotateY(60deg); }

  /* ─ GENERIC CONTENT SLIDE ─ */
  .content-slide {
    max-width: 1100px; width: 100%;
    padding: 3rem 4rem;
    display: grid; gap: 2rem;
  }
  .slide-header { display: flex; flex-direction: column; gap: 0.4rem; }
  .slide-tag {
    font-size: 0.68rem; letter-spacing: 0.18em; text-transform: uppercase;
    color: var(--amber);
  }
  .slide-title {
    font-family: 'Bebas Neue', sans-serif;
    font-size: clamp(2rem, 4vw, 3.2rem);
    color: #fff; line-height: 1;
  }
  .slide-lead {
    font-size: 1rem; color: var(--muted); line-height: 1.7; max-width: 700px;
  }

  /* ─ CARDS GRID ─ */
  .cards { display: grid; gap: 1rem; }
  .cards.c2 { grid-template-columns: 1fr 1fr; }
  .cards.c3 { grid-template-columns: repeat(3,1fr); }
  .cards.c4 { grid-template-columns: repeat(4,1fr); }
  .cards.c5 { grid-template-columns: repeat(5,1fr); }

  .card {
    background: var(--surface);
    border: 1px solid var(--line);
    border-radius: 14px;
    padding: 1.25rem 1.4rem;
    position: relative; overflow: hidden;
    transition: transform 0.3s, border-color 0.3s, box-shadow 0.3s;
  }
  .card:hover {
    transform: translateY(-4px) scale(1.01);
    border-color: rgba(245,166,35,0.4);
    box-shadow: 0 16px 40px rgba(0,0,0,0.4);
  }
  .card::before {
    content:''; position: absolute; top: 0; left: 0; right: 0; height: 2px;
    background: var(--amber); opacity: 0; transition: opacity 0.3s;
  }
  .card:hover::before { opacity: 1; }

  .card-num {
    font-family: 'Bebas Neue', sans-serif;
    font-size: 2.4rem; color: rgba(245,166,35,0.2); line-height: 1;
    margin-bottom: 0.3rem;
  }
  .card-title {
    font-size: 0.78rem; font-weight: 600; text-transform: uppercase;
    letter-spacing: 0.06em; color: var(--amber); margin-bottom: 0.5rem;
  }
  .card-body { font-size: 0.83rem; color: var(--muted); line-height: 1.6; }
  .card-value {
    font-family: 'Bebas Neue', sans-serif;
    font-size: 2.2rem; color: var(--amber); margin-top: 0.5rem;
  }

  .card.highlight {
    background: linear-gradient(135deg, rgba(245,166,35,0.12), rgba(245,166,35,0.04));
    border-color: rgba(245,166,35,0.3);
  }
  .card.surface2 { background: var(--surface2); }

  /* ─ STAT ROW ─ */
  .stat-row {
    display: flex; gap: 1rem;
    background: var(--surface); border: 1px solid var(--line);
    border-radius: 14px; padding: 1rem 1.5rem;
    align-items: center;
  }
  .stat-block { flex: 1; text-align: center; }
  .stat-val { font-family: 'Bebas Neue', sans-serif; font-size: 2rem; color: var(--amber); }
  .stat-lbl { font-size: 0.7rem; color: var(--muted); text-transform: uppercase; letter-spacing: 0.1em; }
  .stat-divider { width: 1px; height: 40px; background: var(--line); }

  /* ─ TABLE ─ */
  .data-table {
    width: 100%; border-collapse: collapse;
    font-size: 0.8rem;
  }
  .data-table th {
    font-size: 0.65rem; text-transform: uppercase; letter-spacing: 0.1em;
    color: var(--amber); padding: 0.5rem 0.75rem;
    border-bottom: 1px solid var(--line); text-align: left;
  }
  .data-table td {
    padding: 0.55rem 0.75rem; color: var(--muted);
    border-bottom: 1px solid rgba(255,255,255,0.04);
  }
  .data-table tr:last-child td { border-bottom: none; }
  .data-table tr:hover td { background: rgba(255,255,255,0.03); color: var(--text); }
  .data-table .pill {
    display: inline-block; padding: 0.15rem 0.55rem;
    border-radius: 20px; font-size: 0.65rem; font-weight: 600;
  }
  .pill-red    { background: rgba(224,82,82,0.15);  color: var(--red);   }
  .pill-amber  { background: rgba(245,166,35,0.15); color: var(--amber); }
  .pill-green  { background: rgba(76,175,126,0.15); color: var(--green); }
  .pill-blue   { background: rgba(79,159,232,0.15); color: var(--blue);  }

  /* ─ PROCESS FLOW ─ */
  .flow { display: flex; align-items: flex-start; gap: 0; }
  .flow-step {
    flex: 1; text-align: center; position: relative; padding: 0 0.5rem;
  }
  .flow-step::after {
    content: '▶'; position: absolute; right: -12px; top: 18px;
    color: var(--amber); font-size: 0.75rem; z-index:1;
  }
  .flow-step:last-child::after { display: none; }
  .flow-circle {
    width: 48px; height: 48px; border-radius: 50%;
    background: var(--surface3); border: 2px solid var(--amber);
    display: flex; align-items: center; justify-content: center;
    margin: 0 auto 0.6rem;
    font-family: 'Bebas Neue', sans-serif; font-size: 1.2rem; color: var(--amber);
  }
  .flow-label { font-size: 0.72rem; color: var(--muted); line-height: 1.4; }

  /* ─ TWO-COL ─ */
  .two-col { display: grid; grid-template-columns: 1fr 1fr; gap: 2rem; align-items: start; }
  .three-col { display: grid; grid-template-columns: 1fr 1fr 1fr; gap: 1.5rem; align-items: start; }

  /* ─ ALERT BOXES ─ */
  .alert {
    border-radius: 10px; padding: 0.9rem 1.1rem;
    display: flex; gap: 0.8rem; align-items: flex-start;
  }
  .alert-icon { font-size: 1.2rem; margin-top: 1px; flex-shrink: 0; }
  .alert-text { font-size: 0.8rem; line-height: 1.6; }
  .alert.green { background: rgba(76,175,126,0.1); border-left: 3px solid var(--green); }
  .alert.amber { background: rgba(245,166,35,0.1); border-left: 3px solid var(--amber); }
  .alert.red   { background: rgba(224,82,82,0.1);  border-left: 3px solid var(--red);   }

  /* ─ SEASONALITY BAR ─ */
  .season-grid { display: grid; grid-template-columns: repeat(12,1fr); gap: 4px; }
  .season-bar { border-radius: 4px; position: relative; min-height: 6px; }
  .season-month { font-size: 0.58rem; color: var(--muted); text-align: center; margin-top: 4px; }
  .season-idx { font-size: 0.6rem; text-align: center; margin-bottom: 4px; }

  /* ─ QUOTE BOX ─ */
  .quote-box {
    background: var(--surface); border: 1px solid var(--line);
    border-left: 4px solid var(--amber);
    border-radius: 0 12px 12px 0;
    padding: 1rem 1.25rem;
    font-size: 0.85rem; color: var(--muted); line-height: 1.7;
    font-style: italic;
  }

  /* ─ TECH STACK BADGE ─ */
  .tech-badge {
    display: inline-flex; align-items: center; gap: 6px;
    background: var(--surface3); border: 1px solid var(--line);
    border-radius: 8px; padding: 0.35rem 0.75rem;
    font-size: 0.75rem; color: var(--text);
  }
  .tech-cost { color: var(--amber); font-weight: 600; }

  /* ─ PHASE TIMELINE ─ */
  .timeline { position: relative; padding-left: 1.5rem; }
  .timeline::before {
    content:''; position: absolute; left: 0; top: 0; bottom: 0;
    width: 2px; background: var(--line);
  }
  .tl-item { position: relative; margin-bottom: 1rem; padding-left: 1.25rem; }
  .tl-item::before {
    content:''; position: absolute; left: -1.45rem; top: 0.35rem;
    width: 10px; height: 10px; border-radius: 50%;
    background: var(--amber); border: 2px solid var(--bg);
  }
  .tl-title { font-size: 0.78rem; font-weight: 600; color: var(--amber); }
  .tl-body  { font-size: 0.75rem; color: var(--muted); line-height: 1.5; }

  /* ─ KPI CARD ─ */
  .kpi-card {
    background: var(--surface); border: 1px solid var(--line);
    border-radius: 12px; padding: 1rem 1.2rem;
    display: flex; align-items: center; gap: 1rem;
  }
  .kpi-icon {
    width: 42px; height: 42px; border-radius: 10px;
    background: rgba(245,166,35,0.12); border: 1px solid rgba(245,166,35,0.3);
    display: flex; align-items: center; justify-content: center;
    font-size: 1.2rem; flex-shrink: 0;
  }
  .kpi-label { font-size: 0.68rem; color: var(--muted); text-transform: uppercase; letter-spacing: 0.08em; }
  .kpi-value { font-family: 'Bebas Neue', sans-serif; font-size: 1.5rem; color: var(--amber); }

  /* ─ PORTAL CARD ─ */
  .portal {
    background: var(--surface); border: 1px solid var(--line);
    border-radius: 16px; padding: 1.5rem;
  }
  .portal-header {
    display: flex; align-items: center; gap: 0.75rem; margin-bottom: 1rem;
  }
  .portal-icon {
    width: 38px; height: 38px; border-radius: 10px;
    background: rgba(245,166,35,0.15); display: flex; align-items: center;
    justify-content: center; font-size: 1.1rem;
  }
  .portal-name { font-size: 0.85rem; font-weight: 600; color: var(--text); }
  .portal-sub  { font-size: 0.7rem; color: var(--muted); }
  .portal ul   { list-style: none; }
  .portal li   { font-size: 0.76rem; color: var(--muted); padding: 0.3rem 0;
                  border-bottom: 1px solid var(--line); display: flex; gap: 0.5rem; }
  .portal li:last-child { border-bottom: none; }
  .portal li::before { content: '→'; color: var(--amber); flex-shrink: 0; }

  /* ─ GRID BACKGROUND ─ */
  .grid-bg {
    position: fixed; inset: 0; pointer-events: none; z-index: 0;
    background-image:
      linear-gradient(rgba(255,255,255,0.025) 1px, transparent 1px),
      linear-gradient(90deg, rgba(255,255,255,0.025) 1px, transparent 1px);
    background-size: 60px 60px;
    mask-image: radial-gradient(ellipse 80% 80% at 50% 50%, black 40%, transparent 100%);
  }

  /* ─ CONCLUSION ROW ─ */
  .concl-row {
    display: grid; grid-template-columns: repeat(4,1fr); gap: 1rem;
  }
  .concl-card {
    background: var(--surface); border: 1px solid var(--line);
    border-radius: 14px; padding: 1.25rem;
    border-top: 3px solid;
  }
  .concl-card:nth-child(1) { border-top-color: var(--amber); }
  .concl-card:nth-child(2) { border-top-color: var(--blue);  }
  .concl-card:nth-child(3) { border-top-color: var(--green); }
  .concl-card:nth-child(4) { border-top-color: var(--red);   }
  .concl-label { font-size: 0.65rem; text-transform: uppercase; letter-spacing: 0.12em; margin-bottom: 0.5rem; }
  .concl-card:nth-child(1) .concl-label { color: var(--amber); }
  .concl-card:nth-child(2) .concl-label { color: var(--blue);  }
  .concl-card:nth-child(3) .concl-label { color: var(--green); }
  .concl-card:nth-child(4) .concl-label { color: var(--red);   }
  .concl-num  { font-family: 'Bebas Neue', sans-serif; font-size: 2.5rem; margin-bottom: 0.3rem; }
  .concl-card:nth-child(1) .concl-num { color: var(--amber); }
  .concl-card:nth-child(2) .concl-num { color: var(--blue);  }
  .concl-card:nth-child(3) .concl-num { color: var(--green); }
  .concl-card:nth-child(4) .concl-num { color: var(--red);   }
  .concl-body { font-size: 0.78rem; color: var(--muted); line-height: 1.6; }

  /* ─ TAG LINE ─ */
  .tagline {
    text-align: center; padding: 3rem 2rem; max-width: 700px;
  }
  .tagline h2 {
    font-family: 'Bebas Neue', sans-serif;
    font-size: clamp(2.5rem, 5vw, 4.5rem);
    color: #fff; line-height: 1.05; margin-bottom: 1rem;
  }
  .tagline h2 em { color: var(--amber); font-style: normal; }
  .tagline p { font-size: 0.95rem; color: var(--muted); line-height: 1.7; }

  /* ─ ANIMATED ENTRANCE ─ */
  .slide.active .card,
  .slide.active .portal,
  .slide.active .stat-row,
  .slide.active .alert,
  .slide.active .kpi-card,
  .slide.active .concl-card,
  .slide.active .tl-item {
    animation: card-in 0.5s both;
  }
  .slide.active .card:nth-child(1),
  .slide.active .portal:nth-child(1),
  .slide.active .kpi-card:nth-child(1),
  .slide.active .concl-card:nth-child(1) { animation-delay: 0.05s; }
  .slide.active .card:nth-child(2),
  .slide.active .portal:nth-child(2),
  .slide.active .kpi-card:nth-child(2),
  .slide.active .concl-card:nth-child(2) { animation-delay: 0.12s; }
  .slide.active .card:nth-child(3),
  .slide.active .portal:nth-child(3),
  .slide.active .kpi-card:nth-child(3),
  .slide.active .concl-card:nth-child(3) { animation-delay: 0.19s; }
  .slide.active .card:nth-child(4),
  .slide.active .kpi-card:nth-child(4),
  .slide.active .concl-card:nth-child(4) { animation-delay: 0.26s; }
  .slide.active .card:nth-child(5),
  .slide.active .kpi-card:nth-child(5) { animation-delay: 0.33s; }
  @keyframes card-in {
    from { opacity: 0; transform: translateY(20px); }
    to   { opacity: 1; transform: translateY(0); }
  }
  .slide.active .slide-title,
  .slide.active .slide-tag,
  .slide.active .slide-lead { animation: fade-up 0.5s both; }
  .slide.active .slide-tag   { animation-delay: 0.0s; }
  .slide.active .slide-title { animation-delay: 0.07s; }
  .slide.active .slide-lead  { animation-delay: 0.14s; }
  @keyframes fade-up {
    from { opacity: 0; transform: translateY(14px); }
    to   { opacity: 1; transform: translateY(0); }
  }

  /* ─ SCROLLABLE INNER if slide content is too tall ─ */
  .scroll-inner {
    max-height: calc(100vh - 10rem);
    overflow-y: auto; padding-right: 0.5rem;
    scrollbar-width: thin; scrollbar-color: var(--surface3) transparent;
  }

  /* ─ KEY CONCEPT BOX ─ */
  .key-concept {
    background: linear-gradient(135deg, rgba(245,166,35,0.08), transparent);
    border: 1px solid rgba(245,166,35,0.25);
    border-radius: 12px; padding: 1rem 1.25rem;
    font-size: 0.82rem; color: var(--muted); line-height: 1.7;
  }
  .key-concept strong { color: var(--amber); }

  /* ─ KEYBOARD HINT ─ */
  #hint {
    position: fixed; bottom: 5.5rem; left: 50%; transform: translateX(-50%);
    font-size: 0.65rem; color: rgba(255,255,255,0.2);
    display: flex; gap: 0.5rem; align-items: center;
    pointer-events: none;
  }
  kbd {
    background: rgba(255,255,255,0.08); border: 1px solid rgba(255,255,255,0.15);
    border-radius: 4px; padding: 1px 6px; font-size: 0.6rem;
  }
</style>
</head>
<body>

<div class="grid-bg"></div>
<div id="prog-bar" style="width:10%"></div>

<div id="badge">
  <span>Ferretería Digital</span> · Subasta Inversa v2.0
</div>

<!-- ════════════════════════════════════════════════════════ SLIDES ═══ -->
<div id="scene">
<div id="track">

<!-- ── 0: COVER ── -->
<div class="slide active" id="s0">
  <div style="transform-style:preserve-3d; perspective:800px; position:relative; text-align:center; padding:3rem;">
    <div class="cube-bg" style="transform-style:preserve-3d;">
      <div class="cube-ring"></div>
      <div class="cube-ring"></div>
      <div class="cube-ring"></div>
    </div>
    <div class="cover-inner">
      <p class="cover-eyebrow">Sistema Integral de Cadena de Abastecimiento</p>
      <h1 class="cover-title">Ferretería<br><em>Digital</em><br>con Subasta Inversa</h1>
      <p class="cover-sub">Análisis profesional con proyecciones de estacionalidad, plan de contingencias y diseño de plataforma digital. Marcos: Chopra & Meindl · Christopher · Ballou.</p>
      <div class="cover-version">
        <span>Versión <strong>2.0</strong></span>
        <span>Año <strong>2025</strong></span>
        <span>Alcance <strong>B2B</strong></span>
      </div>
    </div>
  </div>
</div>

<!-- ── 1: PROPUESTA DE VALOR ── -->
<div class="slide" id="s1">
  <div class="content-slide">
    <div class="slide-header">
      <p class="slide-tag">01 · Resumen Ejecutivo</p>
      <h2 class="slide-title">Propuesta de Valor</h2>
      <p class="slide-lead">La subasta inversa invierte el poder de negociación: los proveedores compiten para ganar al cliente, reduciendo costos de adquisición hasta un <strong style="color:var(--amber)">22%</strong>.</p>
    </div>

    <div class="key-concept">
      <strong>Concepto clave:</strong> La subasta inversa es un proceso en el que múltiples proveedores compiten activamente bajando sus precios para adjudicarse un pedido. El comprador especifica lo que necesita y fija una ventana de tiempo; los vendedores pujan a la baja.
    </div>

    <div class="cards c2">
      <div class="card">
        <p class="card-title">❌ Problema actual</p>
        <ul style="list-style:none;display:flex;flex-direction:column;gap:0.5rem;">
          <li style="font-size:0.78rem;color:var(--muted);padding:0.3rem 0;border-bottom:1px solid var(--line)">Cotizar múltiples proveedores demora días o semanas</li>
          <li style="font-size:0.78rem;color:var(--muted);padding:0.3rem 0;border-bottom:1px solid var(--line)">Precios de lista sin poder de negociación real</li>
          <li style="font-size:0.78rem;color:var(--muted);padding:0.3rem 0;border-bottom:1px solid var(--line)">Sin visibilidad de estacionalidad de la demanda</li>
          <li style="font-size:0.78rem;color:var(--muted);padding:0.3rem 0;border-bottom:1px solid var(--line)">Dependencia de un solo proveedor</li>
          <li style="font-size:0.78rem;color:var(--muted);padding:0.3rem 0">Procesos manuales y en papel</li>
        </ul>
      </div>
      <div class="card highlight">
        <p class="card-title">✅ Solución del sistema</p>
        <ul style="list-style:none;display:flex;flex-direction:column;gap:0.5rem;">
          <li style="font-size:0.78rem;color:var(--muted);padding:0.3rem 0;border-bottom:1px solid var(--line)">Subasta inversa consolida ofertas en <strong style="color:var(--amber)">24–72 horas</strong></li>
          <li style="font-size:0.78rem;color:var(--muted);padding:0.3rem 0;border-bottom:1px solid var(--line)">Competencia directa baja precios <strong style="color:var(--amber)">12–22%</strong></li>
          <li style="font-size:0.78rem;color:var(--muted);padding:0.3rem 0;border-bottom:1px solid var(--line)">Módulo de proyecciones con análisis histórico</li>
          <li style="font-size:0.78rem;color:var(--muted);padding:0.3rem 0;border-bottom:1px solid var(--line)">Cartera diversificada con proveedores validados</li>
          <li style="font-size:0.78rem;color:var(--muted);padding:0.3rem 0">Automatización completa con n8n + Google Workspace</li>
        </ul>
      </div>
    </div>

    <div class="stat-row">
      <div class="stat-block"><div class="stat-val">22%</div><div class="stat-lbl">Ahorro en adquisición</div></div>
      <div class="stat-divider"></div>
      <div class="stat-block"><div class="stat-val">24–72h</div><div class="stat-lbl">Tiempo de subasta</div></div>
      <div class="stat-divider"></div>
      <div class="stat-block"><div class="stat-val">45min</div><div class="stat-lbl">Proceso vs. 8 horas antes</div></div>
      <div class="stat-divider"></div>
      <div class="stat-block"><div class="stat-val">3+</div><div class="stat-lbl">Proveedores por categoría</div></div>
    </div>
  </div>
</div>

<!-- ── 2: MARCO TEÓRICO ── -->
<div class="slide" id="s2">
  <div class="content-slide">
    <div class="slide-header">
      <p class="slide-tag">02 · Fundamentos Teóricos</p>
      <h2 class="slide-title">Tres Marcos de Cadena de Abastecimiento</h2>
      <p class="slide-lead">El sistema integra los referentes académicos más reconocidos en supply chain para ferretería B2B.</p>
    </div>
    <div class="cards c3">
      <div class="card">
        <div class="card-num">01</div>
        <p class="card-title">Chopra & Meindl</p>
        <p class="card-body">Eficiencia vs. Capacidad de Respuesta. El sistema diseña una <em>frontera eficiente dual</em>: subasta inversa para proyectos grandes (eficiencia de costo) y stock de emergencia para pedidos urgentes (respuesta rápida).</p>
      </div>
      <div class="card">
        <div class="card-num">02</div>
        <p class="card-title">Martin Christopher</p>
        <p class="card-body">Cadena Ágil y Lean. Define el <em>punto de desacoplamiento</em>: materiales básicos → estrategia lean; materiales especiales por proyecto → estrategia ágil con subasta por solicitud.</p>
      </div>
      <div class="card">
        <div class="card-num">03</div>
        <p class="card-title">Ronald Ballou</p>
        <p class="card-body">Optimiza las tres variables críticas: reduce costo de pedido (8h → 45min), reduce desabasto con alertas de estacionalidad, y elimina inventario improductivo con datos de rotación por categoría.</p>
      </div>
    </div>

    <div style="margin-top:0.5rem;">
      <p style="font-size:0.68rem;color:var(--muted);text-transform:uppercase;letter-spacing:0.1em;margin-bottom:0.75rem;">Casos de referencia validados</p>
      <div class="cards c4">
        <div class="card surface2" style="padding:0.9rem;">
          <p class="card-title" style="font-size:0.7rem">Home Depot Pro · EE.UU.</p>
          <div class="card-value" style="font-size:1.5rem">−18%</div>
          <p class="card-body" style="font-size:0.72rem">Costo de adquisición</p>
        </div>
        <div class="card surface2" style="padding:0.9rem;">
          <p class="card-title" style="font-size:0.7rem">Sodimac Constructor · Chile/Perú</p>
          <div class="card-value" style="font-size:1.5rem">5d→6h</div>
          <p class="card-body" style="font-size:0.72rem">Tiempo de cotización</p>
        </div>
        <div class="card surface2" style="padding:0.9rem;">
          <p class="card-title" style="font-size:0.7rem">Nacobre · México</p>
          <div class="card-value" style="font-size:1.5rem">−31%</div>
          <p class="card-body" style="font-size:0.72rem">Quiebres de stock</p>
        </div>
        <div class="card surface2" style="padding:0.9rem;">
          <p class="card-title" style="font-size:0.7rem">Ferreterías Kywi · Ecuador</p>
          <div class="card-value" style="font-size:1.5rem">+40%</div>
          <p class="card-body" style="font-size:0.72rem">Pedidos de proyecto grande</p>
        </div>
      </div>
    </div>
  </div>
</div>

<!-- ── 3: ARQUITECTURA ── -->
<div class="slide" id="s3">
  <div class="content-slide">
    <div class="slide-header">
      <p class="slide-tag">03 · Arquitectura del Sistema</p>
      <h2 class="slide-title">5 Módulos Interconectados</h2>
      <p class="slide-lead">Cada módulo tiene responsabilidades bien definidas y tecnología específica asignada.</p>
    </div>
    <div class="cards c5">
      <div class="card">
        <div class="card-num">M1</div>
        <p class="card-title">Portal del Cliente</p>
        <p class="card-body">Solicitud de proyectos, carga de materiales y planos</p>
        <p style="font-size:0.68rem;color:var(--blue);margin-top:0.5rem">Web App PWA + Google Forms</p>
      </div>
      <div class="card">
        <div class="card-num">M2</div>
        <p class="card-title">Motor de Subasta</p>
        <p class="card-body">Orquestación del proceso de puja inversa</p>
        <p style="font-size:0.68rem;color:var(--blue);margin-top:0.5rem">n8n + Google Sheets</p>
      </div>
      <div class="card">
        <div class="card-num">M3</div>
        <p class="card-title">Portal de Proveedores</p>
        <p class="card-body">Recepción de RFQ, envío de ofertas, historial</p>
        <p style="font-size:0.68rem;color:var(--blue);margin-top:0.5rem">Web App + Google Forms</p>
      </div>
      <div class="card highlight">
        <div class="card-num">M4</div>
        <p class="card-title">Análisis & Proyecciones</p>
        <p class="card-body">Dashboard de precios, estacionalidad, alertas</p>
        <p style="font-size:0.68rem;color:var(--amber);margin-top:0.5rem">Looker Studio</p>
      </div>
      <div class="card">
        <div class="card-num">M5</div>
        <p class="card-title">Gestión de Contratos</p>
        <p class="card-body">Generación, firma y seguimiento de pedidos</p>
        <p style="font-size:0.68rem;color:var(--blue);margin-top:0.5rem">PandaDoc + Drive</p>
      </div>
    </div>

    <div>
      <p style="font-size:0.68rem;color:var(--muted);text-transform:uppercase;letter-spacing:0.1em;margin-bottom:0.75rem;">Stack tecnológico · Costo mensual total: <strong style="color:var(--amber)">$21–80 USD/mes</strong></p>
      <div style="display:flex;flex-wrap:wrap;gap:0.5rem;">
        <span class="tech-badge">n8n self-hosted <span class="tech-cost">$5–10</span></span>
        <span class="tech-badge">Google Workspace <span class="tech-cost">$6/usuario</span></span>
        <span class="tech-badge">Looker Studio <span class="tech-cost" style="color:var(--green)">Gratis</span></span>
        <span class="tech-badge">PandaDoc <span class="tech-cost" style="color:var(--green)">$0–19</span></span>
        <span class="tech-badge">Twilio WhatsApp <span class="tech-cost">$10–20</span></span>
        <span class="tech-badge">Glide/Softr PWA <span class="tech-cost">$0–25</span></span>
        <span class="tech-badge">Cloudflare SSL <span class="tech-cost" style="color:var(--green)">Gratis</span></span>
      </div>
    </div>
  </div>
</div>

<!-- ── 4: FLUJO SUBASTA ── -->
<div class="slide" id="s4">
  <div class="content-slide">
    <div class="slide-header">
      <p class="slide-tag">04 · Motor de Subasta Inversa</p>
      <h2 class="slide-title">Flujo Detallado del Proceso</h2>
      <p class="slide-lead">Desde la solicitud del cliente hasta la firma del contrato digital en 8 pasos automatizados.</p>
    </div>

    <div class="flow">
      <div class="flow-step">
        <div class="flow-circle">1</div>
        <p class="flow-label">Cliente crea RFQ con lista de materiales y fecha requerida</p>
      </div>
      <div class="flow-step">
        <div class="flow-circle">2</div>
        <p class="flow-label">n8n valida y clasifica por categoría, activa flujo</p>
      </div>
      <div class="flow-step">
        <div class="flow-circle">3</div>
        <p class="flow-label">Notificación por email + WhatsApp a proveedores habilitados</p>
      </div>
      <div class="flow-step">
        <div class="flow-circle">4</div>
        <p class="flow-label">Proveedores pujan en 24–72h: precio, plazo, stock</p>
      </div>
      <div class="flow-step">
        <div class="flow-circle">5</div>
        <p class="flow-label">n8n consolida y genera ranking ponderado automático</p>
      </div>
      <div class="flow-step">
        <div class="flow-circle">6</div>
        <p class="flow-label">Encargado aprueba adjudicación con un clic en Data Studio</p>
      </div>
      <div class="flow-step">
        <div class="flow-circle">7</div>
        <p class="flow-label">Proveedor ganador recibe contrato digital para firma</p>
      </div>
      <div class="flow-step">
        <div class="flow-circle">8</div>
        <p class="flow-label">Sistema actualiza inventario y activa seguimiento de entrega</p>
      </div>
    </div>

    <div class="key-concept" style="margin-top:0.5rem">
      <strong>Ranking ponderado automático:</strong> Precio 60% + Plazo de entrega 25% + Calificación histórica del proveedor 15%. Genera adjudicaciones objetivas y trazables.
    </div>

    <div class="cards c3" style="margin-top:0.5rem">
      <div class="kpi-card"><div class="kpi-icon">⏱</div><div><div class="kpi-value">24–72h</div><div class="kpi-label">Ventana estándar de subasta</div></div></div>
      <div class="kpi-card"><div class="kpi-icon">📊</div><div><div class="kpi-value">60/25/15</div><div class="kpi-label">Peso: precio / plazo / historial</div></div></div>
      <div class="kpi-card"><div class="kpi-icon">📄</div><div><div class="kpi-value">1 clic</div><div class="kpi-label">Para adjudicar y firmar</div></div></div>
    </div>
  </div>
</div>

<!-- ── 5: ESTACIONALIDAD ── -->
<div class="slide" id="s5">
  <div class="content-slide">
    <div class="slide-header">
      <p class="slide-tag">05 · Proyecciones por Estacionalidad</p>
      <h2 class="slide-title">Mapa Estacional de Demanda</h2>
      <p class="slide-lead">En Perú, el 35–45% de ventas anuales ocurre en el segundo trimestre (Mar–Jun), coincidiendo con inicio de obras post-lluvia y presupuestos municipales.</p>
    </div>

    <div style="background:var(--surface);border:1px solid var(--line);border-radius:14px;padding:1.25rem;">
      <p style="font-size:0.68rem;color:var(--muted);text-transform:uppercase;letter-spacing:0.1em;margin-bottom:1rem">Índices estacionales — modelo 2025</p>
      <div id="season-chart"></div>
    </div>

    <div class="three-col" style="margin-top:0.5rem">
      <div class="alert green">
        <span class="alert-icon">🟢</span>
        <div class="alert-text"><strong style="color:var(--green)">Alerta verde:</strong> Inventario normal, temporada baja. Monitoreo cada 7 días. Oportunidades de compra anticipada a precio bajo.</div>
      </div>
      <div class="alert amber">
        <span class="alert-icon">🟡</span>
        <div class="alert-text"><strong style="color:var(--amber)">Alerta amarilla:</strong> Inventario en umbral mínimo O 6 semanas antes de temporada alta. Lanza subasta inversa automáticamente.</div>
      </div>
      <div class="alert red">
        <span class="alert-icon">🔴</span>
        <div class="alert-text"><strong style="color:var(--red)">Alerta roja:</strong> Inventario crítico O pico activo sin confirmación de entrega. Activa protocolo de contingencia inmediato.</div>
      </div>
    </div>
  </div>
</div>

<!-- ── 6: CONTINGENCIAS ── -->
<div class="slide" id="s6">
  <div class="content-slide">
    <div class="slide-header">
      <p class="slide-tag">06 · Plan de Contingencias</p>
      <h2 class="slide-title">Protocolos ante Riesgos Críticos</h2>
      <p class="slide-lead">Metodología Chopra & Sodhi (HBR 2004) para gestión de riesgos en cadena de suministro B2B.</p>
    </div>

    <div class="cards c2">
      <div class="card">
        <p class="card-title" style="color:var(--red)">C1 — Proveedor no entrega</p>
        <p class="card-body">Activación: 48h sin confirmación. <strong style="color:var(--text)">0–4h:</strong> Alerta automática + proveedor de respaldo. <strong style="color:var(--text)">4–24h:</strong> Nuevo cronograma al cliente + penalidad 2%/día. El proveedor queda suspendido 90 días.</p>
      </div>
      <div class="card">
        <p class="card-title" style="color:var(--amber)">C2 — Sin ofertas suficientes</p>
        <p class="card-body">Menos de 2 ofertas válidas al cierre. n8n extiende la ventana 24h y amplía la notificación a proveedores de segunda línea con mayor radio geográfico. Si persiste: contratación directa con excepción registrada.</p>
      </div>
      <div class="card">
        <p class="card-title" style="color:var(--blue)">C3 — Falla tecnológica</p>
        <p class="card-body">Prevención: todas las solicitudes se replican en Google Sheets. Si n8n falla >2h en horario hábil, el encargado usa plantillas prediseñadas (Procedimiento PR-01). Restauración desde backup diario en Drive.</p>
      </div>
      <div class="card highlight">
        <p class="card-title" style="color:var(--red)">C4 — Rotura de stock en pico</p>
        <p class="card-body">Stock de seguridad: demanda de 14 días en categorías críticas. Al llegar al 20% del stock mínimo: mini-subasta de emergencia con ventana de <strong style="color:var(--amber)">12h</strong> y precio techo +15% sobre histórico.</p>
      </div>
    </div>

    <div>
      <p style="font-size:0.68rem;color:var(--muted);text-transform:uppercase;letter-spacing:0.1em;margin-bottom:0.6rem">KPIs de monitoreo del plan</p>
      <div style="display:grid;grid-template-columns:repeat(3,1fr);gap:0.7rem">
        <div class="kpi-card" style="padding:0.75rem"><div class="kpi-icon" style="width:36px;height:36px;font-size:1rem">📈</div><div><div class="kpi-value" style="font-size:1.2rem">>90%</div><div class="kpi-label">Subastas con ≥2 ofertas</div></div></div>
        <div class="kpi-card" style="padding:0.75rem"><div class="kpi-icon" style="width:36px;height:36px;font-size:1rem">⏱</div><div><div class="kpi-value" style="font-size:1.2rem"><24h</div><div class="kpi-label">Resolución de contingencia</div></div></div>
        <div class="kpi-card" style="padding:0.75rem"><div class="kpi-icon" style="width:36px;height:36px;font-size:1rem">🚚</div><div><div class="kpi-value" style="font-size:1.2rem">>95%</div><div class="kpi-label">Entregas a tiempo</div></div></div>
        <div class="kpi-card" style="padding:0.75rem"><div class="kpi-icon" style="width:36px;height:36px;font-size:1rem">📦</div><div><div class="kpi-value" style="font-size:1.2rem">100%</div><div class="kpi-label">Stock de seguridad activo</div></div></div>
        <div class="kpi-card" style="padding:0.75rem"><div class="kpi-icon" style="width:36px;height:36px;font-size:1rem">🔴</div><div><div class="kpi-value" style="font-size:1.2rem"><5%</div><div class="kpi-label">Activación alerta roja/mes</div></div></div>
        <div class="kpi-card" style="padding:0.75rem"><div class="kpi-icon" style="width:36px;height:36px;font-size:1rem">⭐</div><div><div class="kpi-value" style="font-size:1.2rem">>7.5</div><div class="kpi-label">Score promedio proveedor</div></div></div>
      </div>
    </div>
  </div>
</div>

<!-- ── 7: PLATAFORMA WEB ── -->
<div class="slide" id="s7">
  <div class="content-slide">
    <div class="slide-header">
      <p class="slide-tag">07 · Plataforma Web / App</p>
      <h2 class="slide-title">Tres Portales, Un Sistema</h2>
      <p class="slide-lead">Diseño Mobile First, usabilidad Nielsen, accesible desde smartphones con conexión 3G. Máximo 3 pasos para crear una solicitud.</p>
    </div>
    <div class="cards c3">
      <div class="portal">
        <div class="portal-header">
          <div class="portal-icon">🏗</div>
          <div>
            <div class="portal-name">Portal 1 · Cliente</div>
            <div class="portal-sub">Contratista / Empresa</div>
          </div>
        </div>
        <ul>
          <li>Login con OTP por WhatsApp en &lt;30 seg</li>
          <li>Panel de proyectos y solicitudes activas</li>
          <li>Nueva solicitud en 3 pasos + carga de Excel</li>
          <li>Comparativo visual con mejor precio en verde</li>
          <li>Firma de contrato digital con un toque</li>
          <li>Historial y facturas descargables</li>
        </ul>
      </div>
      <div class="portal">
        <div class="portal-header">
          <div class="portal-icon">🔧</div>
          <div>
            <div class="portal-name">Portal 2 · Proveedor</div>
            <div class="portal-sub">Ferretería / Distribuidor</div>
          </div>
        </div>
        <ul>
          <li>Dashboard de RFQs abiertas en su categoría</li>
          <li>Vista de cada solicitud (cliente anonimizado)</li>
          <li>Formulario de oferta: precio, plazo, condiciones</li>
          <li>Notificación si ganó o perdió + brecha de precio</li>
          <li>Historial de subastas y métricas de desempeño</li>
          <li>Catálogo de productos y precios de referencia</li>
        </ul>
      </div>
      <div class="portal">
        <div class="portal-header">
          <div class="portal-icon">📊</div>
          <div>
            <div class="portal-name">Portal 3 · Administrador</div>
            <div class="portal-sub">Gerente de la Ferretería</div>
          </div>
        </div>
        <ul>
          <li>Dashboard ejecutivo: ventas, márgenes, alertas</li>
          <li>Gestión de proveedores: score e historial</li>
          <li>Control de subastas: apertura, cierre, penalidades</li>
          <li>Reportes de ahorro y tendencias de precios</li>
          <li>Configuración de alertas y umbrales de stock</li>
          <li>Análisis de rotación por categoría y temporada</li>
        </ul>
      </div>
    </div>

    <div class="key-concept">
      <strong>Principios de diseño:</strong> Simplicidad radical (máx. 3 pasos) · Mobile First (pantallas ≥5") · Lenguaje claro sin tecnicismos · Feedback inmediato por SMS/WhatsApp · Accesibilidad offline (PWA) · Soporte para idioma Quechua en interfaz básica.
    </div>
  </div>
</div>

<!-- ── 8: EVALUACIÓN DE PROVEEDORES ── -->
<div class="slide" id="s8">
  <div class="content-slide">
    <div class="slide-header">
      <p class="slide-tag">08 · Gestión de Proveedores</p>
      <h2 class="slide-title">Modelo de Score Compuesto</h2>
      <p class="slide-lead">Score actualizado automáticamente tras cada transacción. Proveedor con score &lt;6.0 queda suspendido; &lt;5.0 inhabilitado del sistema.</p>
    </div>

    <div class="two-col">
      <div>
        <p style="font-size:0.68rem;color:var(--muted);text-transform:uppercase;letter-spacing:0.1em;margin-bottom:0.75rem">Criterios de evaluación ponderada</p>
        <div style="display:flex;flex-direction:column;gap:0.6rem">
          <div style="display:flex;align-items:center;gap:0.75rem">
            <div style="min-width:36px;font-family:'Bebas Neue',sans-serif;font-size:1.3rem;color:var(--amber)">30%</div>
            <div style="flex:1">
              <div style="height:6px;background:var(--surface3);border-radius:4px;overflow:hidden">
                <div style="height:100%;width:30%;background:var(--amber);border-radius:4px"></div>
              </div>
            </div>
            <div style="font-size:0.75rem;color:var(--muted);min-width:160px">Precio competitivo</div>
          </div>
          <div style="display:flex;align-items:center;gap:0.75rem">
            <div style="min-width:36px;font-family:'Bebas Neue',sans-serif;font-size:1.3rem;color:var(--amber)">25%</div>
            <div style="flex:1">
              <div style="height:6px;background:var(--surface3);border-radius:4px;overflow:hidden">
                <div style="height:100%;width:25%;background:var(--blue);border-radius:4px"></div>
              </div>
            </div>
            <div style="font-size:0.75rem;color:var(--muted);min-width:160px">Cumplimiento de plazo</div>
          </div>
          <div style="display:flex;align-items:center;gap:0.75rem">
            <div style="min-width:36px;font-family:'Bebas Neue',sans-serif;font-size:1.3rem;color:var(--amber)">20%</div>
            <div style="flex:1">
              <div style="height:6px;background:var(--surface3);border-radius:4px;overflow:hidden">
                <div style="height:100%;width:20%;background:var(--green);border-radius:4px"></div>
              </div>
            </div>
            <div style="font-size:0.75rem;color:var(--muted);min-width:160px">Calidad del producto</div>
          </div>
          <div style="display:flex;align-items:center;gap:0.75rem">
            <div style="min-width:36px;font-family:'Bebas Neue',sans-serif;font-size:1.3rem;color:var(--amber)">15%</div>
            <div style="flex:1">
              <div style="height:6px;background:var(--surface3);border-radius:4px;overflow:hidden">
                <div style="height:100%;width:15%;background:var(--red);border-radius:4px"></div>
              </div>
            </div>
            <div style="font-size:0.75rem;color:var(--muted);min-width:160px">Disponibilidad de stock</div>
          </div>
          <div style="display:flex;align-items:center;gap:0.75rem">
            <div style="min-width:36px;font-family:'Bebas Neue',sans-serif;font-size:1.3rem;color:var(--amber)">10%</div>
            <div style="flex:1">
              <div style="height:6px;background:var(--surface3);border-radius:4px;overflow:hidden">
                <div style="height:100%;width:10%;background:var(--muted);border-radius:4px"></div>
              </div>
            </div>
            <div style="font-size:0.75rem;color:var(--muted);min-width:160px">Tiempo de respuesta RFQ</div>
          </div>
        </div>
      </div>
      <div>
        <p style="font-size:0.68rem;color:var(--muted);text-transform:uppercase;letter-spacing:0.1em;margin-bottom:0.75rem">Proceso de habilitación</p>
        <div class="timeline">
          <div class="tl-item">
            <p class="tl-title">Postulación en línea</p>
            <p class="tl-body">RUC, datos de contacto, categorías de producto y referencias comerciales</p>
          </div>
          <div class="tl-item">
            <p class="tl-title">Verificación automática SUNAT</p>
            <p class="tl-body">n8n consulta RUC y situación tributaria en tiempo real</p>
          </div>
          <div class="tl-item">
            <p class="tl-title">Visita o videollamada</p>
            <p class="tl-body">Checklist de 10 puntos de habilitación por el encargado</p>
          </div>
          <div class="tl-item">
            <p class="tl-title">Acceso al portal</p>
            <p class="tl-body">Clasificado en sus categorías; acceso inmediato a RFQs</p>
          </div>
          <div class="tl-item">
            <p class="tl-title">Monitoreo reforzado (3 meses)</p>
            <p class="tl-body">Revisión de cada entrega; score calculado desde transacción 1</p>
          </div>
        </div>

        <div class="alert amber" style="margin-top:1rem">
          <span class="alert-icon">⚙️</span>
          <div class="alert-text">Mínimo <strong>3 proveedores habilitados por categoría</strong> para garantizar competencia real. Al menos 1 local (rapidez) + 1 nacional/importador (precio bajo).</div>
        </div>
      </div>
    </div>
  </div>
</div>

<!-- ── 9: IMPLEMENTACIÓN ── -->
<div class="slide" id="s9">
  <div class="content-slide">
    <div class="slide-header">
      <p class="slide-tag">09 · Plan de Implementación</p>
      <h2 class="slide-title">6 Fases · 14 Semanas · ~$700–1,500 USD</h2>
      <p class="slide-lead">Implementación progresiva comenzando por las 3 categorías de mayor volumen.</p>
    </div>

    <div class="cards c3">
      <div class="card">
        <div class="card-num">F0</div>
        <p class="card-title">Diagnóstico · 2 sem</p>
        <p class="card-body">Mapeo de proveedores actuales, datos históricos de ventas, definición de categorías prioritarias.</p>
        <div class="card-value" style="font-size:1.3rem;color:var(--green)">$0</div>
      </div>
      <div class="card">
        <div class="card-num">F1</div>
        <p class="card-title">Infraestructura · 1 sem</p>
        <p class="card-body">VPS, instalación n8n, Google Workspace y dominio web configurados.</p>
        <div class="card-value" style="font-size:1.3rem">$50–100</div>
      </div>
      <div class="card">
        <div class="card-num">F2</div>
        <p class="card-title">Formularios & Flujos · 3 sem</p>
        <p class="card-body">Diseño de RFQ y oferta, flujos n8n, pruebas con 5 proveedores piloto.</p>
        <div class="card-value" style="font-size:1.3rem">$0–200</div>
      </div>
      <div class="card">
        <div class="card-num">F3</div>
        <p class="card-title">Portal Web · 2 sem</p>
        <p class="card-body">Configuración Glide/Softr, catálogo digital, dashboards en Looker Studio.</p>
        <div class="card-value" style="font-size:1.3rem">$0–150</div>
      </div>
      <div class="card highlight">
        <div class="card-num">F4</div>
        <p class="card-title">Piloto · 4 sem</p>
        <p class="card-body">Primera subasta real con 3 proyectos de bajo riesgo. Ajuste de flujos y score.</p>
        <div class="card-value" style="font-size:1.3rem;color:var(--green)">$0</div>
      </div>
      <div class="card">
        <div class="card-num">F5</div>
        <p class="card-title">Escala · 2 sem</p>
        <p class="card-body">Todos los proyectos al sistema, capacitación del equipo, alertas de estacionalidad activas.</p>
        <div class="card-value" style="font-size:1.3rem;color:var(--green)">$0</div>
      </div>
    </div>

    <div>
      <p style="font-size:0.68rem;color:var(--muted);text-transform:uppercase;letter-spacing:0.1em;margin-bottom:0.7rem">Métricas de éxito — objetivo a 6 meses</p>
      <div style="display:grid;grid-template-columns:repeat(3,1fr);gap:0.7rem">
        <div class="kpi-card" style="padding:0.75rem"><div class="kpi-icon" style="width:36px;height:36px;font-size:1rem">⏱</div><div><div style="font-size:0.7rem;color:var(--muted)">Tiempo de cotización</div><div style="font-size:0.78rem;color:var(--text)">3–7 días → <strong style="color:var(--amber)">&lt;72h</strong></div></div></div>
        <div class="kpi-card" style="padding:0.75rem"><div class="kpi-icon" style="width:36px;height:36px;font-size:1rem">💰</div><div><div style="font-size:0.7rem;color:var(--muted)">Reducción de costo</div><div style="font-size:0.78rem;color:var(--text)">0% → <strong style="color:var(--amber)">10–18%</strong></div></div></div>
        <div class="kpi-card" style="padding:0.75rem"><div class="kpi-icon" style="width:36px;height:36px;font-size:1rem">📦</div><div><div style="font-size:0.7rem;color:var(--muted)">Quiebres de stock</div><div style="font-size:0.78rem;color:var(--text)">Variable → <strong style="color:var(--amber)">&lt;5%</strong></div></div></div>
        <div class="kpi-card" style="padding:0.75rem"><div class="kpi-icon" style="width:36px;height:36px;font-size:1rem">🏢</div><div><div style="font-size:0.7rem;color:var(--muted)">Proveedores/categoría</div><div style="font-size:0.78rem;color:var(--text)">1–2 → <strong style="color:var(--amber)">≥3</strong></div></div></div>
        <div class="kpi-card" style="padding:0.75rem"><div class="kpi-icon" style="width:36px;height:36px;font-size:1rem">📈</div><div><div style="font-size:0.7rem;color:var(--muted)">Proyectos cotizados</div><div style="font-size:0.78rem;color:var(--text)">Base → <strong style="color:var(--amber)">+40%</strong></div></div></div>
        <div class="kpi-card" style="padding:0.75rem"><div class="kpi-icon" style="width:36px;height:36px;font-size:1rem">⭐</div><div><div style="font-size:0.7rem;color:var(--muted)">NPS del cliente</div><div style="font-size:0.78rem;color:var(--text)">No medido → <strong style="color:var(--amber)">&gt;7.5/10</strong></div></div></div>
      </div>
    </div>
  </div>
</div>

<!-- ── 10: CONCLUSIONES ── -->
<div class="slide" id="s10">
  <div class="content-slide">
    <div class="slide-header">
      <p class="slide-tag">10 · Conclusiones</p>
      <h2 class="slide-title">De Ferretería Reactiva a Plataforma Proactiva</h2>
      <p class="slide-lead">El sistema genera beneficios concretos en cuatro dimensiones con ROI positivo desde el tercer mes de operación.</p>
    </div>

    <div class="concl-row">
      <div class="concl-card">
        <p class="concl-label">Beneficio Económico</p>
        <p class="concl-num">22%</p>
        <p class="concl-body">Ahorro en costos de adquisición en los primeros 12 meses. ROI positivo desde el mes 3.</p>
      </div>
      <div class="concl-card">
        <p class="concl-label">Beneficio Operativo</p>
        <p class="concl-num">10h</p>
        <p class="concl-body">Reducción de carga administrativa semanal. El equipo se enfoca en atención al cliente y nuevos proyectos.</p>
      </div>
      <div class="concl-card">
        <p class="concl-label">Beneficio Estratégico</p>
        <p class="concl-num">∞</p>
        <p class="concl-body">Historial acumulado de precios y proyecciones: activo estratégico único para negociar contratos anuales.</p>
      </div>
      <div class="concl-card">
        <p class="concl-label">Inversión Total</p>
        <p class="concl-num">$1.5K</p>
        <p class="concl-body">Primer año estimado incluyendo infraestructura, herramientas y consultoría opcional.</p>
      </div>
    </div>

    <div class="key-concept">
      <strong>Recomendaciones clave:</strong> Comenzar con las 3 categorías de mayor volumen → Habilitar mínimo 3 proveedores por categoría antes del lanzamiento → Capacitar al encargado en Data Studio → Calibrar índices de estacionalidad con datos reales al cierre del primer año → Incluir cláusulas de penalidad desde el inicio.
    </div>

    <div style="text-align:center;margin-top:1rem;padding:1rem;background:var(--surface);border:1px solid rgba(245,166,35,0.2);border-radius:14px">
      <p style="font-size:0.7rem;color:var(--muted);text-transform:uppercase;letter-spacing:0.12em;margin-bottom:0.5rem">Marco bibliográfico</p>
      <p style="font-size:0.75rem;color:var(--muted)">Chopra & Meindl (2019) · Christopher (2016) · Ballou (2004) · Chopra & Sodhi HBR (2004) · Nielsen (1994)</p>
    </div>
  </div>
</div>

</div><!-- /track -->
</div><!-- /scene -->

<!-- ── NAVIGATION ── -->
<div id="dots"></div>

<div id="nav">
  <button id="btn-prev" aria-label="Diapositiva anterior">‹</button>
  <span id="counter">1 / 11</span>
  <button id="btn-next" aria-label="Diapositiva siguiente">›</button>
</div>

<p id="hint"><kbd>←</kbd> <kbd>→</kbd> para navegar · <kbd>F</kbd> para pantalla completa</p>

<script>
const TOTAL = 11;
let cur = 0;

const slides = document.querySelectorAll('.slide');
const dots   = document.getElementById('dots');
const counter= document.getElementById('counter');
const prog   = document.getElementById('prog-bar');
const track  = document.getElementById('track');

/* Build dots */
for(let i=0;i<TOTAL;i++){
  const d=document.createElement('div');
  d.className='dot'+(i===0?' active':'');
  d.addEventListener('click',()=>goTo(i));
  dots.appendChild(d);
}

function goTo(n){
  slides[cur].classList.remove('active');
  dots.children[cur].classList.remove('active');
  cur=Math.max(0,Math.min(TOTAL-1,n));
  slides[cur].classList.add('active');
  dots.children[cur].classList.add('active');
  counter.textContent=`${cur+1} / ${TOTAL}`;
  prog.style.width=`${((cur+1)/TOTAL)*100}%`;

  // 3D slide transition
  const angle = cur * -5;
  track.style.transform = `translateZ(-120px) rotateY(${angle * 0.5}deg)`;
  setTimeout(()=>{ track.style.transform='none'; track.style.transition='none';
    setTimeout(()=>{ track.style.transition='transform 0.8s cubic-bezier(0.77,0,0.175,1)'; },50);
  },50);
}

document.getElementById('btn-prev').addEventListener('click',()=>goTo(cur-1));
document.getElementById('btn-next').addEventListener('click',()=>goTo(cur+1));

document.addEventListener('keydown',e=>{
  if(e.key==='ArrowRight'||e.key==='ArrowDown') goTo(cur+1);
  if(e.key==='ArrowLeft'||e.key==='ArrowUp')   goTo(cur-1);
  if(e.key==='f'||e.key==='F') document.fullscreenElement?document.exitFullscreen():document.documentElement.requestFullscreen();
});

/* Touch/swipe */
let tx=0;
document.addEventListener('touchstart',e=>{ tx=e.touches[0].clientX; });
document.addEventListener('touchend',e=>{
  const dx=e.changedTouches[0].clientX-tx;
  if(dx<-50) goTo(cur+1);
  if(dx>50)  goTo(cur-1);
});

/* ── SEASONALITY CHART ── */
const months = ['Ene','Feb','Mar','Abr','May','Jun','Jul','Ago','Sep','Oct','Nov','Dic'];
const idx    = [0.72,0.78,1.18,1.35,1.40,1.28,0.90,0.88,1.05,1.15,1.10,0.80];
const levels = ['green','green','amber','red','red','red','green','green','amber','amber','amber','green'];
const col    = { green:'#4CAF7E', amber:'#F5A623', red:'#E05252' };

function buildSeasonChart(){
  const c = document.getElementById('season-chart');
  if(!c) return;
  const maxH = 80;
  let html = '<div style="display:grid;grid-template-columns:repeat(12,1fr);gap:6px;align-items:end;height:'+(maxH+30)+'px">';
  for(let i=0;i<12;i++){
    const h = Math.round(idx[i]/1.5*maxH);
    html += `<div style="display:flex;flex-direction:column;align-items:center;gap:3px">
      <span style="font-size:0.58rem;color:${col[levels[i]]}">${idx[i].toFixed(2)}</span>
      <div style="width:100%;height:${h}px;background:${col[levels[i]]};border-radius:4px 4px 0 0;opacity:0.85"></div>
      <span style="font-size:0.58rem;color:var(--muted)">${months[i]}</span>
    </div>`;
  }
  html += '</div>';
  html += '<div style="display:flex;gap:1rem;margin-top:0.75rem;font-size:0.65rem;color:var(--muted)">';
  html += '<span><span style="color:#4CAF7E">■</span> Temporada baja (verde)</span>';
  html += '<span><span style="color:#F5A623">■</span> Preparación (amarillo)</span>';
  html += '<span><span style="color:#E05252">■</span> Pico (rojo)</span>';
  html += '</div>';
  c.innerHTML = html;
}
buildSeasonChart();
</script>
</body>
</html>
