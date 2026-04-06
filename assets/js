/**
 * auto-rating.js
 * Drop this file in your project folder and add ONE line to every blog post HTML:
 *   <script src="auto-rating.js"></script>
 * That's it. The widget appears automatically on every page.
 */

(function () {
  // ── Auto-generate a unique key per page using the URL path ──
  const STORAGE_KEY = 'blog_rating::' + window.location.pathname;

  // ── Inject Google Fonts ──────────────────────────────────────
  const font = document.createElement('link');
  font.rel  = 'stylesheet';
  font.href = 'https://fonts.googleapis.com/css2?family=Lora:wght@400;600&family=DM+Sans:wght@300;400;500&display=swap';
  document.head.appendChild(font);

  // ── Inject CSS ───────────────────────────────────────────────
  const style = document.createElement('style');
  style.textContent = `
    .auto-rating-widget {
      max-width: 680px;
      margin: 60px auto 40px;
      font-family: 'DM Sans', sans-serif;
    }
    .auto-rating-divider {
      width: 100%;
      height: 1px;
      background: linear-gradient(to right, transparent, #ddd 20%, #ddd 80%, transparent);
      margin-bottom: 48px;
    }
    .auto-rating-card {
      display: flex;
      flex-direction: column;
      align-items: center;
      gap: 20px;
      padding: 40px 32px;
      background: #fff;
      border: 1px solid #e8e3db;
      border-radius: 16px;
      box-shadow: 0 4px 32px rgba(0,0,0,0.05);
    }
    .ar-label {
      font-family: 'Lora', serif;
      font-size: 20px;
      color: #111;
      text-align: center;
    }
    .ar-sublabel {
      font-size: 13px;
      color: #999;
      text-align: center;
      margin-top: 4px;
    }
    .ar-stars {
      display: flex;
      gap: 8px;
      cursor: pointer;
    }
    .ar-star {
      font-size: 36px;
      color: #ddd;
      transition: color 0.15s, transform 0.15s;
      user-select: none;
      line-height: 1;
    }
    .ar-star.hovered, .ar-star.selected { color: #e8a020; transform: scale(1.15); }
    .ar-star.selected { transform: scale(1.0); }
    .ar-btn {
      padding: 10px 28px;
      background: #111;
      color: #fff;
      border: none;
      border-radius: 8px;
      font-family: 'DM Sans', sans-serif;
      font-size: 14px;
      font-weight: 500;
      cursor: pointer;
      transition: background 0.2s, transform 0.1s;
      display: none;
    }
    .ar-btn:hover { background: #333; }
    .ar-btn:active { transform: scale(0.97); }
    .ar-btn.visible { display: inline-block; }
    .ar-thanks {
      text-align: center;
      animation: arFadeUp 0.4s ease both;
      display: none;
    }
    .ar-thanks.visible { display: block; }
    .ar-thanks .ar-check { font-size: 36px; margin-bottom: 8px; }
    .ar-thanks p { font-size: 15px; color: #555; }
    .ar-aggregate { width: 100%; max-width: 320px; }
    .ar-agg-header { display: flex; justify-content: space-between; align-items: baseline; margin-bottom: 12px; }
    .ar-avg { font-family: 'Lora', serif; font-size: 32px; color: #111; }
    .ar-count { font-size: 12px; color: #aaa; }
    .ar-bars { display: flex; flex-direction: column; gap: 6px; }
    .ar-bar-row { display: flex; align-items: center; gap: 8px; font-size: 12px; color: #999; }
    .ar-bar-row .ar-bar-lbl { width: 12px; text-align: right; }
    .ar-bar-bg { flex: 1; height: 6px; background: #eee; border-radius: 99px; overflow: hidden; }
    .ar-bar-fill { height: 100%; background: #e8a020; border-radius: 99px; transition: width 0.6s cubic-bezier(.4,0,.2,1); width: 0%; }
    .ar-bar-pct { width: 28px; text-align: right; }
    @keyframes arFadeUp {
      from { opacity: 0; transform: translateY(10px); }
      to   { opacity: 1; transform: translateY(0); }
    }
  `;
  document.head.appendChild(style);

  // ── Build HTML ───────────────────────────────────────────────
  const wrapper = document.createElement('div');
  wrapper.className = 'auto-rating-widget';
  wrapper.innerHTML = `
    <div class="auto-rating-divider"></div>
    <div class="auto-rating-card">
      <div class="ar-input-state">
        <p class="ar-label">Did you enjoy this article?</p>
        <p class="ar-sublabel">Tap a star to rate</p>
        <div class="ar-stars">
          <span class="ar-star" data-value="1">★</span>
          <span class="ar-star" data-value="2">★</span>
          <span class="ar-star" data-value="3">★</span>
          <span class="ar-star" data-value="4">★</span>
          <span class="ar-star" data-value="5">★</span>
        </div>
        <button class="ar-btn">Submit rating</button>
      </div>
      <div class="ar-thanks">
        <div class="ar-check">✦</div>
        <p><strong>Thank you for rating!</strong></p>
        <p>Your feedback helps improve future articles.</p>
      </div>
      <div class="ar-aggregate">
        <div class="ar-agg-header">
          <span class="ar-avg">—</span>
          <span class="ar-count">No ratings yet</span>
        </div>
        <div class="ar-bars"></div>
      </div>
    </div>
  `;

  // ── Wait for DOM then append ─────────────────────────────────
  function mount() {
    // Append after <article>, <main>, or <body> — whichever exists first
    const target = document.querySelector('article') || document.querySelector('main') || document.body;
    target.appendChild(wrapper);
    initWidget();
  }

  if (document.readyState === 'loading') {
    document.addEventListener('DOMContentLoaded', mount);
  } else {
    mount();
  }

  // ── Widget Logic ─────────────────────────────────────────────
  function initWidget() {
    const stars      = wrapper.querySelectorAll('.ar-star');
    const btn        = wrapper.querySelector('.ar-btn');
    const inputState = wrapper.querySelector('.ar-input-state');
    const thanks     = wrapper.querySelector('.ar-thanks');
    const avgEl      = wrapper.querySelector('.ar-avg');
    const countEl    = wrapper.querySelector('.ar-count');
    const barsEl     = wrapper.querySelector('.ar-bars');
    let selected     = 0;

    function loadData() {
      try { return JSON.parse(localStorage.getItem(STORAGE_KEY)) || { counts:[0,0,0,0,0], userVote:null }; }
      catch { return { counts:[0,0,0,0,0], userVote:null }; }
    }
    function saveData(d) { localStorage.setItem(STORAGE_KEY, JSON.stringify(d)); }

    function renderAggregate(data) {
      const total = data.counts.reduce((a,b)=>a+b,0);
      const sum   = data.counts.reduce((s,c,i)=>s+c*(i+1),0);
      const avg   = total ? (sum/total).toFixed(1) : null;
      avgEl.textContent   = avg || '—';
      countEl.textContent = total===0?'No ratings yet':total===1?'1 rating':`${total} ratings`;
      barsEl.innerHTML    = '';
      for (let i=4;i>=0;i--) {
        const pct = total ? Math.round((data.counts[i]/total)*100) : 0;
        barsEl.innerHTML += `
          <div class="ar-bar-row">
            <span class="ar-bar-lbl">${i+1}</span>
            <div class="ar-bar-bg"><div class="ar-bar-fill" data-pct="${pct}"></div></div>
            <span class="ar-bar-pct">${pct}%</span>
          </div>`;
      }
      requestAnimationFrame(()=>{
        wrapper.querySelectorAll('.ar-bar-fill').forEach(el=>{ el.style.width=el.dataset.pct+'%'; });
      });
    }

    stars.forEach(star=>{
      star.addEventListener('mouseenter',()=>{
        const v=+star.dataset.value;
        stars.forEach(s=>s.classList.toggle('hovered',+s.dataset.value<=v));
      });
      star.addEventListener('mouseleave',()=>stars.forEach(s=>s.classList.remove('hovered')));
      star.addEventListener('click',()=>{
        selected=+star.dataset.value;
        stars.forEach(s=>s.classList.toggle('selected',+s.dataset.value<=selected));
        btn.classList.add('visible');
      });
    });

    btn.addEventListener('click',()=>{
      if (!selected) return;
      const data=loadData();
      if (data.userVote) return;
      data.counts[selected-1]++;
      data.userVote=selected;
      saveData(data);
      inputState.style.display='none';
      thanks.classList.add('visible');
      renderAggregate(data);
    });

    // Init
    const data=loadData();
    renderAggregate(data);
    if (data.userVote) {
      inputState.style.display='none';
      thanks.classList.add('visible');
      stars.forEach(s=>s.classList.toggle('selected',+s.dataset.value<=data.userVote));
    }
  }
})();
