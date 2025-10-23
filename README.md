<!-- index.html -->
<!doctype html>
<html lang="pt-BR">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>Você caiu no Phishing</title>
  <style>
    :root{
      --bg-color: #000;
      --text-color: #fff;
      --overlay-bg: rgba(0,0,0,0.45);
      --panel-bg: rgba(255,255,255,0.06);
    }

    html,body{
      height:100%;
      margin:0;
      font-family: Inter, system-ui, -apple-system, "Segoe UI", Roboto, "Helvetica Neue", Arial;
      background: var(--bg-color);
      color: var(--text-color);
    }

    .full {
      min-height:100vh;
      display:flex;
      align-items:center;
      justify-content:center;
      text-align:center;
      position:relative;
      overflow:hidden;
    }

    /* mensagem central */
    .card {
      position:relative;
      z-index:2;
      padding:2rem;
      max-width:900px;
      width:94%;
      border-radius:12px;
      background: linear-gradient(180deg, rgba(0,0,0,0.25), rgba(0,0,0,0.35));
      box-shadow: 0 10px 40px rgba(0,0,0,0.6);
      backdrop-filter: blur(4px);
    }

    h1{
      margin:0 0 0.6rem 0;
      font-size: clamp(28px, 5vw, 56px);
      letter-spacing: -0.02em;
      line-height:1.02;
      color: #fff;
      text-transform: none;
    }

    p.lead{
      margin:0 0 1rem 0;
      font-size: clamp(14px, 2.2vw, 18px);
      opacity:0.95;
    }

    .controls{
      margin-top:1rem;
      display:flex;
      gap:0.5rem;
      justify-content:center;
      flex-wrap:wrap;
    }

    button, a.btn {
      border: none;
      padding:0.6rem 1rem;
      border-radius:8px;
      cursor:pointer;
      font-weight:600;
      color:#111;
      background: #fff;
      text-decoration:none;
      display:inline-flex;
      gap:0.5rem;
      align-items:center;
    }

    button.secondary {
      background: linear-gradient(180deg,#eee,#ddd);
      color:#111;
    }

    .warning {
      margin-top:0.8rem;
      font-size:13px;
      color:#ffeb3b;
    }

    .small {
      font-size:13px;
      opacity:0.9;
      margin-top:0.6rem;
      color:#ddd;
    }

    
    .rainbow {
      position:absolute;
      inset:0;
      z-index:1;
      pointer-events:none;
      mix-blend-mode: screen;
      transition:opacity 300ms linear;
    }

    
    .stripe {
      position:absolute;
      inset:0;
      opacity:0;
      transition:opacity 220ms linear;
      background: linear-gradient(120deg, transparent, rgba(255,255,255,0.08));
    }

    
    .c-red   { background: linear-gradient(90deg, rgba(255,58,48,0.95), rgba(255,90,80,0.65)); }
    .c-orange{ background: linear-gradient(90deg, rgba(255,134,0,0.95), rgba(255,172,39,0.65)); }
    .c-yellow{ background: linear-gradient(90deg, rgba(255,214,0,0.95), rgba(255,230,90,0.65)); }
    .c-green { background: linear-gradient(90deg, rgba(0,200,83,0.95), rgba(39,220,140,0.65)); }
    .c-blue  { background: linear-gradient(90deg, rgba(3,169,244,0.95), rgba(30,200,255,0.65)); }
    .c-indigo{ background: linear-gradient(90deg, rgba(63,81,181,0.95), rgba(100,120,220,0.65)); }
    .c-violet{ background: linear-gradient(90deg, rgba(156,39,176,0.95), rgba(190,90,215,0.65)); }

    
    .stripe.show { opacity: 1; }

    
    @media (prefers-reduced-motion: reduce) {
      .stripe { transition:none !important; }
    }

  
    .footer {
      position: absolute;
      bottom: 14px;
      left: 12px;
      right: 12px;
      z-index:3;
      color: #ddd;
      font-size:12px;
      display:flex;
      justify-content:space-between;
      gap:12px;
      align-items:center;
    }

    .logo {
      display:inline-block;
      background:rgba(255,255,255,0.06);
      padding:6px 10px;
      border-radius:8px;
      font-weight:600;
      color:#fff;
      font-size:13px;
    }

   
    @media (max-width:420px){
      .controls{ flex-direction:column; }
      button, a.btn { width:100%; justify-content:center; }
    }

  </style>
</head>
<body>
  <main class="full" role="main">
    <div class="rainbow" aria-hidden="true">
      <div class="stripe c-red"    data-color="red"></div>
      <div class="stripe c-orange" data-color="orange"></div>
      <div class="stripe c-yellow" data-color="yellow"></div>
      <div class="stripe c-green"  data-color="green"></div>
      <div class="stripe c-blue"   data-color="blue"></div>
      <div class="stripe c-indigo" data-color="indigo"></div>
      <div class="stripe c-violet" data-color="violet"></div>
    </div>

    <section class="card" role="article" aria-labelledby="title">
  <h1 id="title">Você caiu em um Phishing</h1>
  <p class="lead">Fique em paz e só um teste .</p>

  <div class="controls" role="group" aria-label="Controles">
    <button id="toggle" aria-pressed="true" title="Pausar animação">Parar animação</button>
    
  </div>

  
  <div class="progress-bar-container" aria-hidden="true" style="margin-top: 1.5rem;">
    <div class="progress-bar"></div>
  </div>
</section>

    

    

    <div class="footer" aria-hidden="false">
      <div style="opacity:.9"> <span id="year"></span></div>
    </div>
  </main>

  <script>
    // Cores em sequência
    const stripes = Array.from(document.querySelectorAll('.stripe'));
    const toggleBtn = document.getElementById('toggle');
    const yearSpan = document.getElementById('year');
    yearSpan.textContent = new Date().getFullYear();

    
    const reduceMotion = window.matchMedia('(prefers-reduced-motion: reduce)').matches;

    let current = 0;
    let intervalId = null;
    let running = !reduceMotion; 

    function showColor(index) {
      stripes.forEach((s, i) => {
        s.classList.toggle('show', i === index);
      });
    }

    function startCycle() {
      if (intervalId) return;
     
      intervalId = setInterval(() => {
        current = (current + 1) % stripes.length;
        showColor(current);
      }, 450);
      toggleBtn.textContent = 'Parar animação';
      toggleBtn.setAttribute('aria-pressed','true');
      running = true;
    }

    function stopCycle() {
      clearInterval(intervalId);
      intervalId = null;
      stripes.forEach(s => s.classList.remove('show'));
      toggleBtn.textContent = 'Retomar animação';
      toggleBtn.setAttribute('aria-pressed','false');
      running = false;
    }

    toggleBtn.addEventListener('click', (e) => {
      if (running) stopCycle();
      else startCycle();
      
      toggleBtn.focus();
    });

    
    if (!reduceMotion) {
      
      showColor(current);
      startCycle();
    } else {
      
      toggleBtn.textContent = 'Retomar animação';
      toggleBtn.setAttribute('aria-pressed','false');
    }

    
    window.addEventListener('keydown', (ev) => {
      if (ev.key === 'Escape') {
        stopCycle();
      }
    });

    
    const warning = document.getElementById('warning');
    const observer = new MutationObserver(() => {
      warning.setAttribute('aria-live','polite');
    });
    observer.observe(toggleBtn, { attributes: true });

  </script>
</body>
</html>
