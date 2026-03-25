<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>My Public IP</title>
  <link href="https://fonts.googleapis.com/css2?family=Share+Tech+Mono&family=Orbitron:wght@400;700;900&display=swap" rel="stylesheet"/>
  <style>
    *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }

    :root {
      --bg: #020b14;
      --panel: #051a2e;
      --accent: #00ffe5;
      --accent2: #0af;
      --accent3: #ff9f00;
      --dim: #0a3a52;
      --text: #cceeff;
      --muted: #4a7a99;
      --danger: #ff4466;
      --success: #00ff88;
    }

    body {
      background: var(--bg);
      color: var(--text);
      font-family: 'Share Tech Mono', monospace;
      min-height: 100vh;
      display: flex;
      align-items: center;
      justify-content: center;
      overflow-x: hidden;
      position: relative;
    }

    body::before {
      content: '';
      position: fixed;
      inset: 0;
      background-image:
        linear-gradient(rgba(0,255,229,0.03) 1px, transparent 1px),
        linear-gradient(90deg, rgba(0,255,229,0.03) 1px, transparent 1px);
      background-size: 40px 40px;
      animation: gridMove 20s linear infinite;
      pointer-events: none;
    }

    @keyframes gridMove {
      0% { background-position: 0 0; }
      100% { background-position: 40px 40px; }
    }

    body::after {
      content: '';
      position: fixed;
      inset: 0;
      background: radial-gradient(ellipse 80% 60% at 50% 50%, rgba(0,170,255,0.06) 0%, transparent 70%);
      pointer-events: none;
    }

    .scanline {
      position: fixed;
      top: 0; left: 0; right: 0;
      height: 2px;
      background: rgba(0,255,229,0.05);
      animation: scan 7s linear infinite;
      pointer-events: none;
      z-index: 100;
    }

    @keyframes scan {
      0% { top: -2px; }
      100% { top: 100vh; }
    }

    .container {
      position: relative;
      z-index: 10;
      text-align: center;
      padding: 24px 16px;
      width: 100%;
      max-width: 860px;
    }

    .header-label {
      font-size: 0.6rem;
      letter-spacing: 0.45em;
      color: var(--muted);
      text-transform: uppercase;
      margin-bottom: 10px;
      animation: fadeUp 0.8s ease forwards;
    }

    .status-dot {
      display: inline-block;
      width: 6px; height: 6px;
      border-radius: 50%;
      background: var(--accent);
      box-shadow: 0 0 8px var(--accent);
      animation: pulse 2s ease infinite;
      margin-right: 8px;
      vertical-align: middle;
    }

    @keyframes pulse {
      0%, 100% { opacity: 1; transform: scale(1); }
      50% { opacity: 0.4; transform: scale(0.7); }
    }

    .main-title {
      font-family: 'Orbitron', monospace;
      font-size: clamp(1rem, 3vw, 1.3rem);
      font-weight: 900;
      letter-spacing: 0.2em;
      color: var(--accent);
      text-transform: uppercase;
      margin-bottom: 40px;
      animation: fadeUp 1s ease forwards;
    }

    @keyframes fadeUp {
      from { opacity: 0; transform: translateY(12px); }
      to { opacity: 1; transform: translateY(0); }
    }

    .cards {
      display: grid;
      grid-template-columns: 1fr 1fr;
      gap: 20px;
      margin-bottom: 24px;
    }

    @media (max-width: 620px) {
      .cards { grid-template-columns: 1fr; }
    }

    .ip-card {
      position: relative;
      border: 1px solid var(--dim);
      border-radius: 4px;
      padding: 32px 24px 28px;
      background: var(--panel);
      box-shadow:
        0 0 40px rgba(0,170,255,0.06),
        inset 0 0 40px rgba(0,0,0,0.3);
      opacity: 0;
      transform: translateY(20px);
      animation: cardIn 1s cubic-bezier(0.16,1,0.3,1) forwards;
    }

    .ip-card.v4 { animation-delay: 0.2s; --card-accent: var(--accent); }
    .ip-card.v6 { animation-delay: 0.4s; --card-accent: var(--accent3); }

    @keyframes cardIn {
      to { opacity: 1; transform: translateY(0); }
    }

    .ip-card::before, .ip-card::after {
      content: '';
      position: absolute;
      width: 12px; height: 12px;
      border-color: var(--card-accent);
      border-style: solid;
    }
    .ip-card::before { top: -1px; left: -1px; border-width: 2px 0 0 2px; }
    .ip-card::after  { bottom: -1px; right: -1px; border-width: 0 2px 2px 0; }

    .card-badge {
      font-family: 'Orbitron', monospace;
      font-size: 0.6rem;
      font-weight: 700;
      letter-spacing: 0.3em;
      text-transform: uppercase;
      color: var(--card-accent);
      margin-bottom: 18px;
      display: flex;
      align-items: center;
      justify-content: center;
      gap: 8px;
    }

    .badge-line {
      flex: 1;
      height: 1px;
      background: linear-gradient(90deg, transparent, var(--card-accent));
      opacity: 0.3;
    }
    .badge-line.right {
      background: linear-gradient(90deg, var(--card-accent), transparent);
    }

    .ip-value {
      font-family: 'Orbitron', monospace;
      font-weight: 900;
      color: var(--card-accent);
      word-break: break-all;
      line-height: 1.3;
      min-height: 2.6em;
      display: flex;
      align-items: center;
      justify-content: center;
      transition: all 0.4s ease;
    }

    .ip-value.v4-val { font-size: clamp(1.4rem, 4vw, 2.4rem); letter-spacing: 0.04em; }
    .ip-value.v6-val { font-size: clamp(0.75rem, 2vw, 1.1rem); letter-spacing: 0.06em; }

    .ip-value.glow-v4 { text-shadow: 0 0 20px rgba(0,255,229,0.6), 0 0 50px rgba(0,255,229,0.2); }
    .ip-value.glow-v6 { text-shadow: 0 0 20px rgba(255,159,0,0.6), 0 0 50px rgba(255,159,0,0.2); }

    .ip-value.loading {
      font-size: 0.9rem;
      color: var(--muted);
      letter-spacing: 0.2em;
      animation: blink 1s step-end infinite;
      text-shadow: none;
    }

    .ip-value.unavail {
      font-size: 0.8rem;
      color: var(--muted);
      text-shadow: none;
      letter-spacing: 0.1em;
    }

    @keyframes blink {
      0%, 100% { opacity: 1; }
      50% { opacity: 0.2; }
    }

    .divider {
      width: 100%; height: 1px;
      background: linear-gradient(90deg, transparent, var(--dim), transparent);
      margin: 20px 0;
    }

    .card-meta {
      display: flex;
      justify-content: space-between;
      gap: 8px;
      flex-wrap: wrap;
    }

    .meta-item { text-align: left; }
    .meta-key {
      font-size: 0.55rem;
      letter-spacing: 0.25em;
      color: var(--muted);
      text-transform: uppercase;
      margin-bottom: 3px;
    }
    .meta-val {
      font-size: 0.72rem;
      color: var(--card-accent);
      letter-spacing: 0.04em;
    }

    .copy-btn {
      margin-top: 18px;
      width: 100%;
      background: transparent;
      border: 1px solid var(--dim);
      color: var(--muted);
      font-family: 'Share Tech Mono', monospace;
      font-size: 0.65rem;
      letter-spacing: 0.2em;
      text-transform: uppercase;
      padding: 9px 0;
      cursor: pointer;
      border-radius: 2px;
      transition: all 0.2s ease;
    }

    .copy-btn:hover:not(:disabled) {
      border-color: var(--card-accent);
      color: var(--card-accent);
      box-shadow: 0 0 16px rgba(0,255,229,0.1);
    }

    .copy-btn.copied {
      border-color: var(--success);
      color: var(--success);
      background: rgba(0,255,136,0.06);
    }

    .copy-btn:disabled {
      opacity: 0.3;
      cursor: not-allowed;
    }

    .summary-bar {
      border: 1px solid var(--dim);
      border-radius: 3px;
      padding: 14px 24px;
      background: rgba(5,26,46,0.6);
      display: flex;
      align-items: center;
      justify-content: center;
      gap: 32px;
      flex-wrap: wrap;
      opacity: 0;
      animation: fadeUp 1s ease 0.8s forwards;
    }

    .summary-item {
      display: flex;
      align-items: center;
      gap: 8px;
      font-size: 0.65rem;
      letter-spacing: 0.1em;
      color: var(--muted);
    }

    .dot-v4 { width:7px;height:7px;border-radius:50%;background:var(--accent);box-shadow:0 0 6px var(--accent); flex-shrink:0; }
    .dot-v6 { width:7px;height:7px;border-radius:50%;background:var(--accent3);box-shadow:0 0 6px var(--accent3); flex-shrink:0; }

    .footer {
      margin-top: 20px;
      font-size: 0.58rem;
      color: var(--muted);
      letter-spacing: 0.15em;
      opacity: 0;
      animation: fadeUp 1s ease 1.1s forwards;
    }
  </style>
</head>
<body>
<div class="scanline"></div>

<div class="container">
  <div class="header-label"><span class="status-dot"></span> Network Identity Terminal</div>
  <div class="main-title">Public IP Detector</div>

  <div class="cards">

    <!-- IPv4 Card -->
    <div class="ip-card v4">
      <div class="card-badge">
        <div class="badge-line"></div>
        IPv4
        <div class="badge-line right"></div>
      </div>
      <div class="ip-value v4-val loading" id="ipv4Display">DETECTING...</div>
      <div class="divider"></div>
      <div class="card-meta">
        <div class="meta-item">
          <div class="meta-key">Protocol</div>
          <div class="meta-val">TCP/IP v4</div>
        </div>
        <div class="meta-item">
          <div class="meta-key">Format</div>
          <div class="meta-val">32-bit</div>
        </div>
        <div class="meta-item">
          <div class="meta-key">Status</div>
          <div class="meta-val" id="v4Status">—</div>
        </div>
      </div>
      <button class="copy-btn" id="copyV4" onclick="copyIP('v4')" disabled>[ Copy IPv4 ]</button>
    </div>

    <!-- IPv6 Card -->
    <div class="ip-card v6">
      <div class="card-badge">
        <div class="badge-line"></div>
        IPv6
        <div class="badge-line right"></div>
      </div>
      <div class="ip-value v6-val loading" id="ipv6Display">DETECTING...</div>
      <div class="divider"></div>
      <div class="card-meta">
        <div class="meta-item">
          <div class="meta-key">Protocol</div>
          <div class="meta-val">TCP/IP v6</div>
        </div>
        <div class="meta-item">
          <div class="meta-key">Format</div>
          <div class="meta-val">128-bit</div>
        </div>
        <div class="meta-item">
          <div class="meta-key">Status</div>
          <div class="meta-val" id="v6Status">—</div>
        </div>
      </div>
      <button class="copy-btn" id="copyV6" onclick="copyIP('v6')" disabled>[ Copy IPv6 ]</button>
    </div>

  </div>

  <div class="summary-bar">
    <div class="summary-item"><div class="dot-v4"></div> IPv4 — 32-bit · 4 octets · ~4.3B addresses</div>
    <div class="summary-item"><div class="dot-v6"></div> IPv6 — 128-bit · 8 groups · 340 undecillion addresses</div>
  </div>

  <div class="footer">IP addresses shown are your real public-facing internet addresses</div>
</div>

<script>
  let ips = { v4: null, v6: null };

  async function fetchIPv4() {
    const el = document.getElementById('ipv4Display');
    const status = document.getElementById('v4Status');
    const apis = [
      { url: 'https://api.ipify.org?format=json', parse: async r => (await r.json()).ip },
      { url: 'https://api4.ipify.org?format=json', parse: async r => (await r.json()).ip },
      { url: 'https://ipv4.icanhazip.com', parse: async r => (await r.text()).trim() },
    ];
    for (const api of apis) {
      try {
        const res = await fetch(api.url, { signal: AbortSignal.timeout(6000) });
        const ip = await api.parse(res);
        if (ip && ip.includes('.')) {
          el.classList.remove('loading');
          el.classList.add('glow-v4');
          el.textContent = ip;
          status.textContent = '🟢 Active';
          ips.v4 = ip;
          document.getElementById('copyV4').disabled = false;
          return;
        }
      } catch(e) { continue; }
    }
    el.classList.remove('loading');
    el.classList.add('unavail');
    el.textContent = 'NOT AVAILABLE';
    status.textContent = '⚪ N/A';
  }

  async function fetchIPv6() {
    const el = document.getElementById('ipv6Display');
    const status = document.getElementById('v6Status');
    const apis = [
      { url: 'https://api6.ipify.org?format=json', parse: async r => (await r.json()).ip },
      { url: 'https://ipv6.icanhazip.com', parse: async r => (await r.text()).trim() },
      { url: 'https://v6.ident.me', parse: async r => (await r.text()).trim() },
    ];
    for (const api of apis) {
      try {
        const res = await fetch(api.url, { signal: AbortSignal.timeout(6000) });
        const ip = await api.parse(res);
        if (ip && ip.includes(':')) {
          el.classList.remove('loading');
          el.classList.add('glow-v6');
          el.textContent = ip;
          status.textContent = '🟢 Active';
          ips.v6 = ip;
          document.getElementById('copyV6').disabled = false;
          return;
        }
      } catch(e) { continue; }
    }
    el.classList.remove('loading');
    el.classList.add('unavail');
    el.textContent = 'NOT SUPPORTED';
    status.textContent = '⚪ N/A';
  }

  function copyIP(ver) {
    const ip = ips[ver];
    const btnId = ver === 'v4' ? 'copyV4' : 'copyV6';
    const btn = document.getElementById(btnId);
    const label = ver === 'v4' ? '[ Copy IPv4 ]' : '[ Copy IPv6 ]';
    if (!ip) return;
    navigator.clipboard.writeText(ip).then(() => {
      btn.textContent = '[ ✓ Copied! ]';
      btn.classList.add('copied');
      setTimeout(() => {
        btn.textContent = label;
        btn.classList.remove('copied');
      }, 2000);
    });
  }

  fetchIPv4();
  fetchIPv6();
</script>
</body>
</html>
