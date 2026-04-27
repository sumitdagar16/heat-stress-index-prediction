<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Heat Stress Index</title>
<link href="https://fonts.googleapis.com/css2?family=Bebas+Neue&family=DM+Mono:wght@400;500&family=DM+Sans:wght@300;400;500&display=swap" rel="stylesheet">
<style>
  :root {
    --bg: #0a0a0a;
    --surface: #111111;
    --surface2: #1a1a1a;
    --border: rgba(255,255,255,0.07);
    --text: #f0ede8;
    --muted: #6b6b6b;
    --accent: #ff4d1c;

    --safe-color: #22c55e;
    --caution-color: #facc15;
    --extreme-caution-color: #f97316;
    --danger-color: #ef4444;
    --extreme-danger-color: #dc2626;
  }

  * { margin: 0; padding: 0; box-sizing: border-box; }

  body {
    background: var(--bg);
    color: var(--text);
    font-family: 'DM Sans', sans-serif;
    min-height: 100vh;
    overflow-x: hidden;
  }

  /* Animated heat shimmer background */
  body::before {
    content: '';
    position: fixed;
    inset: 0;
    background:
      radial-gradient(ellipse 60% 40% at 80% 20%, rgba(255,77,28,0.08) 0%, transparent 60%),
      radial-gradient(ellipse 40% 60% at 20% 80%, rgba(239,68,68,0.05) 0%, transparent 60%);
    pointer-events: none;
    z-index: 0;
  }

  .page-wrapper {
    position: relative;
    z-index: 1;
    max-width: 900px;
    margin: 0 auto;
    padding: 3rem 2rem 5rem;
  }

  /* Header */
  .header {
    display: flex;
    align-items: flex-end;
    justify-content: space-between;
    margin-bottom: 3rem;
    border-bottom: 1px solid var(--border);
    padding-bottom: 1.5rem;
  }

  .brand {
    display: flex;
    flex-direction: column;
    gap: 2px;
  }

  .brand-label {
    font-family: 'DM Mono', monospace;
    font-size: 10px;
    letter-spacing: 0.2em;
    color: var(--accent);
    text-transform: uppercase;
  }

  .brand-title {
    font-family: 'Bebas Neue', sans-serif;
    font-size: 3.2rem;
    line-height: 1;
    letter-spacing: 0.02em;
    color: var(--text);
  }

  .brand-title span {
    color: var(--accent);
  }

  .header-meta {
    text-align: right;
    font-family: 'DM Mono', monospace;
    font-size: 11px;
    color: var(--muted);
    line-height: 1.8;
  }

  /* Input grid */
  .input-section {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 1.5rem;
    margin-bottom: 2rem;
  }

  .input-block {
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: 4px;
    padding: 1.25rem 1.5rem;
    transition: border-color 0.2s;
  }

  .input-block:hover {
    border-color: rgba(255,255,255,0.14);
  }

  .input-block label {
    display: block;
    font-family: 'DM Mono', monospace;
    font-size: 10px;
    letter-spacing: 0.15em;
    text-transform: uppercase;
    color: var(--muted);
    margin-bottom: 0.75rem;
  }

  .input-row {
    display: flex;
    align-items: center;
    gap: 0.75rem;
  }

  .input-block input[type="range"] {
    flex: 1;
    -webkit-appearance: none;
    height: 2px;
    background: var(--surface2);
    outline: none;
    border-radius: 2px;
    cursor: pointer;
  }

  .input-block input[type="range"]::-webkit-slider-thumb {
    -webkit-appearance: none;
    width: 14px;
    height: 14px;
    border-radius: 50%;
    background: var(--accent);
    cursor: pointer;
    border: 2px solid var(--bg);
    transition: transform 0.15s;
  }

  .input-block input[type="range"]::-webkit-slider-thumb:hover {
    transform: scale(1.2);
  }

  .input-value {
    font-family: 'Bebas Neue', sans-serif;
    font-size: 1.6rem;
    color: var(--text);
    min-width: 60px;
    text-align: right;
    line-height: 1;
  }

  .input-value .unit {
    font-family: 'DM Mono', monospace;
    font-size: 10px;
    color: var(--muted);
    margin-left: 2px;
    letter-spacing: 0.05em;
  }

  .toggle-unit {
    background: transparent;
    border: 1px solid var(--border);
    color: var(--muted);
    font-family: 'DM Mono', monospace;
    font-size: 10px;
    letter-spacing: 0.1em;
    padding: 4px 8px;
    border-radius: 2px;
    cursor: pointer;
    transition: all 0.15s;
    margin-top: 0.5rem;
  }

  .toggle-unit:hover {
    border-color: var(--accent);
    color: var(--accent);
  }

  /* Full-width block */
  .input-block.full {
    grid-column: 1 / -1;
  }

  /* Result panel */
  .result-panel {
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: 4px;
    padding: 2rem;
    margin-bottom: 1.5rem;
    position: relative;
    overflow: hidden;
    transition: border-color 0.4s;
  }

  .result-panel::before {
    content: '';
    position: absolute;
    top: 0; left: 0; right: 0;
    height: 3px;
    background: var(--risk-color, var(--muted));
    transition: background 0.4s;
  }

  .result-grid {
    display: grid;
    grid-template-columns: auto 1fr auto;
    gap: 2rem;
    align-items: center;
  }

  .hi-display {
    text-align: center;
  }

  .hi-label {
    font-family: 'DM Mono', monospace;
    font-size: 10px;
    letter-spacing: 0.2em;
    text-transform: uppercase;
    color: var(--muted);
    margin-bottom: 4px;
  }

  .hi-value {
    font-family: 'Bebas Neue', sans-serif;
    font-size: 5rem;
    line-height: 1;
    color: var(--risk-color, var(--text));
    transition: color 0.4s;
  }

  .hi-unit {
    font-family: 'DM Mono', monospace;
    font-size: 12px;
    color: var(--muted);
  }

  .risk-info {
    border-left: 1px solid var(--border);
    padding-left: 2rem;
  }

  .risk-badge {
    display: inline-block;
    font-family: 'Bebas Neue', sans-serif;
    font-size: 1.5rem;
    letter-spacing: 0.05em;
    color: var(--risk-color, var(--text));
    transition: color 0.4s;
    margin-bottom: 0.5rem;
  }

  .risk-description {
    font-size: 13px;
    color: var(--muted);
    line-height: 1.6;
    max-width: 320px;
  }

  .risk-icon {
    font-size: 3rem;
    opacity: 0.8;
    filter: grayscale(0.3);
  }

  /* Gauge bar */
  .gauge-section {
    margin-bottom: 1.5rem;
  }

  .gauge-bar-wrapper {
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: 4px;
    padding: 1.25rem 1.5rem;
  }

  .gauge-label {
    font-family: 'DM Mono', monospace;
    font-size: 10px;
    letter-spacing: 0.15em;
    text-transform: uppercase;
    color: var(--muted);
    margin-bottom: 0.75rem;
    display: flex;
    justify-content: space-between;
  }

  .gauge-track {
    height: 8px;
    border-radius: 4px;
    background: linear-gradient(to right,
      #22c55e 0%,
      #22c55e 20%,
      #facc15 20%,
      #facc15 40%,
      #f97316 40%,
      #f97316 60%,
      #ef4444 60%,
      #ef4444 80%,
      #dc2626 80%,
      #dc2626 100%
    );
    position: relative;
    margin-bottom: 0.5rem;
  }

  .gauge-needle {
    position: absolute;
    top: 50%;
    transform: translateX(-50%) translateY(-50%);
    width: 14px;
    height: 14px;
    background: white;
    border-radius: 50%;
    border: 2px solid var(--bg);
    box-shadow: 0 0 0 2px white;
    transition: left 0.5s cubic-bezier(0.34, 1.56, 0.64, 1);
  }

  .gauge-ticks {
    display: flex;
    justify-content: space-between;
    font-family: 'DM Mono', monospace;
    font-size: 9px;
    color: var(--muted);
    letter-spacing: 0.05em;
  }

  /* Alerts */
  .alerts-section {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 1rem;
  }

  .alert-card {
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: 4px;
    padding: 1rem 1.25rem;
    opacity: 0.4;
    transition: opacity 0.3s, border-color 0.3s;
  }

  .alert-card.active {
    opacity: 1;
    border-color: var(--alert-color);
  }

  .alert-card-header {
    display: flex;
    align-items: center;
    gap: 8px;
    margin-bottom: 0.5rem;
  }

  .alert-dot {
    width: 7px;
    height: 7px;
    border-radius: 50%;
    background: var(--alert-color, var(--muted));
    flex-shrink: 0;
  }

  .alert-card.active .alert-dot {
    animation: pulse 1.5s ease-in-out infinite;
  }

  @keyframes pulse {
    0%, 100% { opacity: 1; transform: scale(1); }
    50% { opacity: 0.5; transform: scale(1.3); }
  }

  .alert-title {
    font-family: 'DM Mono', monospace;
    font-size: 10px;
    letter-spacing: 0.12em;
    text-transform: uppercase;
    color: var(--text);
  }

  .alert-text {
    font-size: 12px;
    color: var(--muted);
    line-height: 1.6;
  }

  /* Footer */
  .footer {
    margin-top: 3rem;
    padding-top: 1.5rem;
    border-top: 1px solid var(--border);
    font-family: 'DM Mono', monospace;
    font-size: 10px;
    color: var(--muted);
    display: flex;
    justify-content: space-between;
    letter-spacing: 0.1em;
  }

  @media (max-width: 600px) {
    .input-section { grid-template-columns: 1fr; }
    .result-grid { grid-template-columns: 1fr; gap: 1rem; }
    .risk-info { border-left: none; padding-left: 0; border-top: 1px solid var(--border); padding-top: 1rem; }
    .alerts-section { grid-template-columns: 1fr; }
    .brand-title { font-size: 2.2rem; }
    .input-block.full { grid-column: 1; }
  }
</style>
</head>
<body>
<div class="page-wrapper">

  <!-- Header -->
  <header class="header">
    <div class="brand">
      <span class="brand-label">Environmental Health Tool</span>
      <h1 class="brand-title">HEAT <span>STRESS</span><br>INDEX</h1>
    </div>
    <div class="header-meta">
      <div id="live-time">--:--:--</div>
      <div>NOAA / NWS FORMULA</div>
      <div>Rothfusz Regression</div>
    </div>
  </header>

  <!-- Inputs -->
  <section class="input-section">
    <div class="input-block">
      <label>Temperature</label>
      <div class="input-row">
        <input type="range" id="temp" min="70" max="130" value="95" step="1">
        <div class="input-value"><span id="temp-display">95</span><span class="unit" id="temp-unit">°F</span></div>
      </div>
      <button class="toggle-unit" id="toggle-unit-btn">Switch to °C</button>
    </div>

    <div class="input-block">
      <label>Relative Humidity</label>
      <div class="input-row">
        <input type="range" id="humidity" min="0" max="100" value="60" step="1">
        <div class="input-value"><span id="humidity-display">60</span><span class="unit">%</span></div>
      </div>
    </div>

    <div class="input-block full">
      <label>Sun Exposure &amp; Wind Adjustment</label>
      <div style="display:flex; gap:1rem; flex-wrap:wrap;">
        <label style="display:flex; align-items:center; gap:8px; cursor:pointer; font-family:'DM Mono',monospace; font-size:11px; color:var(--muted); letter-spacing:0.05em; text-transform:none;">
          <input type="checkbox" id="direct-sun" style="accent-color:var(--accent);">
          Direct sunlight (+10–15°F apparent temp)
        </label>
        <label style="display:flex; align-items:center; gap:8px; cursor:pointer; font-family:'DM Mono',monospace; font-size:11px; color:var(--muted); letter-spacing:0.05em; text-transform:none;">
          <input type="checkbox" id="wind-calm" style="accent-color:var(--accent);">
          Calm / no wind (−1 to +3°F, based on humidity)
        </label>
      </div>
    </div>
  </section>

  <!-- Result -->
  <div class="result-panel" id="result-panel">
    <div class="result-grid">
      <div class="hi-display">
        <div class="hi-label">Heat Index</div>
        <div class="hi-value" id="hi-value">--</div>
        <div class="hi-unit" id="hi-unit-label">°F</div>
      </div>
      <div class="risk-info">
        <div class="risk-badge" id="risk-level">--</div>
        <div class="risk-description" id="risk-desc">Enter temperature and humidity to calculate heat stress risk.</div>
      </div>
      <div class="risk-icon" id="risk-icon">🌡</div>
    </div>
  </div>

  <!-- Gauge -->
  <div class="gauge-section">
    <div class="gauge-bar-wrapper">
      <div class="gauge-label">
        <span>Risk Scale</span>
        <span id="gauge-pct-label">—</span>
      </div>
      <div class="gauge-track">
        <div class="gauge-needle" id="gauge-needle" style="left: 0%;"></div>
      </div>
      <div class="gauge-ticks">
        <span>Safe</span>
        <span>Caution</span>
        <span>Extreme<br>Caution</span>
        <span>Danger</span>
        <span>Extreme<br>Danger</span>
      </div>
    </div>
  </div>

  <!-- Alerts -->
  <div class="alerts-section">
    <div class="alert-card" id="alert-hydration" style="--alert-color: #38bdf8;">
      <div class="alert-card-header">
        <div class="alert-dot"></div>
        <div class="alert-title">Hydration Alert</div>
      </div>
      <div class="alert-text" id="alert-hydration-text">Monitor fluid intake.</div>
    </div>
    <div class="alert-card" id="alert-activity" style="--alert-color: #fb923c;">
      <div class="alert-card-header">
        <div class="alert-dot"></div>
        <div class="alert-title">Activity Advisory</div>
      </div>
      <div class="alert-text" id="alert-activity-text">Normal precautions apply.</div>
    </div>
    <div class="alert-card" id="alert-vulnerable" style="--alert-color: #f472b6;">
      <div class="alert-card-header">
        <div class="alert-dot"></div>
        <div class="alert-title">Vulnerable Groups</div>
      </div>
      <div class="alert-text" id="alert-vulnerable-text">Standard recommendations.</div>
    </div>
    <div class="alert-card" id="alert-emergency" style="--alert-color: #ef4444;">
      <div class="alert-card-header">
        <div class="alert-dot"></div>
        <div class="alert-title">Emergency Signs</div>
      </div>
      <div class="alert-text" id="alert-emergency-text">Know the warning signs of heat exhaustion and heat stroke.</div>
    </div>
  </div>

  <div class="footer">
    <span>BASED ON NOAA ROTHFUSZ REGRESSION EQUATION</span>
    <span>HEAT STRESS INDEX TOOL v1.0</span>
  </div>
</div>

<script>
  // ─── State ───────────────────────────────────────────────────────────────
  let isCelsius = false;

  // ─── DOM refs ────────────────────────────────────────────────────────────
  const tempSlider    = document.getElementById('temp');
  const humiditySlider= document.getElementById('humidity');
  const tempDisplay   = document.getElementById('temp-display');
  const humidityDisp  = document.getElementById('humidity-display');
  const toggleBtn     = document.getElementById('toggle-unit-btn');
  const tempUnitSpan  = document.getElementById('temp-unit');
  const sunCheck      = document.getElementById('direct-sun');
  const windCheck     = document.getElementById('wind-calm');

  const hiValueEl     = document.getElementById('hi-value');
  const hiUnitLabel   = document.getElementById('hi-unit-label');
  const riskLevelEl   = document.getElementById('risk-level');
  const riskDescEl    = document.getElementById('risk-desc');
  const riskIconEl    = document.getElementById('risk-icon');
  const resultPanel   = document.getElementById('result-panel');
  const gaugeNeedle   = document.getElementById('gauge-needle');
  const gaugePctLabel = document.getElementById('gauge-pct-label');

  // ─── NOAA Rothfusz Heat Index (°F) ───────────────────────────────────────
  function calcHeatIndex(T, RH) {
    // Steadman simple formula first
    let HI = 0.5 * (T + 61.0 + (T - 68.0) * 1.2 + RH * 0.094);
    if ((HI + T) / 2 >= 80) {
      // Full Rothfusz regression
      HI = -42.379
        + 2.04901523  * T
        + 10.14333127 * RH
        - 0.22475541  * T * RH
        - 0.00683783  * T * T
        - 0.05481717  * RH * RH
        + 0.00122874  * T * T * RH
        + 0.00085282  * T * RH * RH
        - 0.00000199  * T * T * RH * RH;

      // Adjustments
      if (RH < 13 && T >= 80 && T <= 112) {
        HI -= ((13 - RH) / 4) * Math.sqrt((17 - Math.abs(T - 95)) / 17);
      } else if (RH > 85 && T >= 80 && T <= 87) {
        HI += ((RH - 85) / 10) * ((87 - T) / 5);
      }
    }
    return Math.round(HI * 10) / 10;
  }

  // ─── Risk categories ─────────────────────────────────────────────────────
  function getRisk(hi) {
    if (hi < 80)  return { level: 'Safe',            color: '#22c55e', icon: '✓', pct: 5,  desc: 'Conditions are within normal range. Stay aware and hydrate regularly.' };
    if (hi < 91)  return { level: 'Caution',         color: '#facc15', icon: '⚠', pct: 25, desc: 'Fatigue possible with prolonged exposure and physical activity. Take breaks.' };
    if (hi < 103) return { level: 'Extreme Caution', color: '#f97316', icon: '⚑', pct: 45, desc: 'Heat cramps and heat exhaustion possible with prolonged activity. Limit exertion.' };
    if (hi < 125) return { level: 'Danger',          color: '#ef4444', icon: '⚡', pct: 70, desc: 'Heat cramps and exhaustion likely. Heatstroke possible with continued exposure.' };
    return         { level: 'Extreme Danger',         color: '#dc2626', icon: '☠', pct: 95, desc: 'Heatstroke highly likely. Immediate action required — seek cool environment.' };
  }

  // ─── Alerts content ──────────────────────────────────────────────────────
  function getAlerts(hi, riskLevel) {
    const alerts = {
      hydration: { active: false, text: 'Monitor fluid intake.' },
      activity:  { active: false, text: 'Normal precautions apply.' },
      vulnerable:{ active: false, text: 'Standard recommendations.' },
      emergency: { active: false, text: 'Know the warning signs of heat exhaustion and heat stroke.' }
    };
    if (hi >= 80) {
      alerts.hydration = { active: true, text: 'Drink water every 15–20 min even if not thirsty. Avoid alcohol and caffeine.' };
    }
    if (hi >= 91) {
      alerts.activity  = { active: true, text: 'Avoid strenuous outdoor activity. Rest in shade every 30 min.' };
      alerts.vulnerable= { active: true, text: 'Elderly, children, and those with chronic illness face significantly higher risk.' };
    }
    if (hi >= 103) {
      alerts.activity  = { active: true, text: 'Postpone outdoor work or exercise. Work in cooled environments only.' };
      alerts.emergency = { active: true, text: 'Watch for: heavy sweating, weakness, cold/pale/clammy skin, weak pulse, nausea. Call emergency if suspected heatstroke.' };
    }
    if (hi >= 125) {
      alerts.emergency = { active: true, text: '🚨 EMERGENCY: Stop all outdoor activity. Apply ice packs to neck, armpits, groin. Call emergency services immediately.' };
    }
    return alerts;
  }

  // ─── Update UI ───────────────────────────────────────────────────────────
  function update() {
    const rawTemp = parseFloat(tempSlider.value);
    const RH      = parseFloat(humiditySlider.value);

    // Convert slider value (always °F internally) to display
    const displayTemp = isCelsius ? Math.round((rawTemp - 32) * 5 / 9 * 10) / 10 : rawTemp;
    tempDisplay.textContent = displayTemp;

    let T_F = rawTemp; // always Fahrenheit for formula
    if (sunCheck.checked)  T_F += 10;
    if (windCheck.checked) T_F += (RH > 85 ? 2 : -1);

    let hi_F = calcHeatIndex(T_F, RH);
    let hi_display = isCelsius ? Math.round((hi_F - 32) * 5 / 9 * 10) / 10 : hi_F;

    const risk = getRisk(hi_F);

    // Update result
    hiValueEl.textContent   = hi_display;
    hiUnitLabel.textContent = isCelsius ? '°C' : '°F';
    riskLevelEl.textContent = risk.level;
    riskDescEl.textContent  = risk.desc;
    riskIconEl.textContent  = risk.icon;

    // Color theming via CSS var on panel
    resultPanel.style.setProperty('--risk-color', risk.color);
    riskLevelEl.style.color = risk.color;
    hiValueEl.style.color   = risk.color;

    // Gauge
    gaugeNeedle.style.left  = risk.pct + '%';
    gaugePctLabel.textContent = risk.level.toUpperCase();

    // Alerts
    const alerts = getAlerts(hi_F);
    const alertMap = [
      ['alert-hydration', 'alert-hydration-text', alerts.hydration],
      ['alert-activity',  'alert-activity-text',  alerts.activity],
      ['alert-vulnerable','alert-vulnerable-text', alerts.vulnerable],
      ['alert-emergency', 'alert-emergency-text',  alerts.emergency],
    ];
    alertMap.forEach(([cardId, textId, data]) => {
      const card = document.getElementById(cardId);
      const text = document.getElementById(textId);
      text.textContent = data.text;
      if (data.active) card.classList.add('active');
      else card.classList.remove('active');
    });
  }

  // ─── Unit toggle ─────────────────────────────────────────────────────────
  toggleBtn.addEventListener('click', () => {
    isCelsius = !isCelsius;
    if (isCelsius) {
      tempSlider.min   = Math.round((70 - 32) * 5 / 9);
      tempSlider.max   = Math.round((130 - 32) * 5 / 9);
      const curC = Math.round((parseFloat(tempSlider.value) - 32) * 5 / 9);
      tempSlider.min   = '21';
      tempSlider.max   = '54';
      tempSlider.value = curC;
      tempUnitSpan.textContent = '°C';
      toggleBtn.textContent    = 'Switch to °F';
      // Remap slider to Celsius
      tempSlider.addEventListener('input', sliderC);
      tempSlider.removeEventListener('input', sliderF);
    } else {
      const curF = Math.round(parseFloat(tempSlider.value) * 9 / 5 + 32);
      tempSlider.min   = '70';
      tempSlider.max   = '130';
      tempSlider.value = curF;
      tempUnitSpan.textContent = '°F';
      toggleBtn.textContent    = 'Switch to °C';
      tempSlider.addEventListener('input', sliderF);
      tempSlider.removeEventListener('input', sliderC);
    }
    update();
  });

  function sliderF() {
    // Slider is directly in °F
    tempDisplay.textContent = this.value;
  }
  function sliderC() {
    // Slider is in °C, value stored as °C, convert for formula
    tempDisplay.textContent = this.value;
  }

  // Override update for Celsius slider (value is °C, needs conversion)
  const originalUpdate = update;
  function update() {
    const rawSlider = parseFloat(tempSlider.value);
    const RH        = parseFloat(humiditySlider.value);

    let T_F = isCelsius ? rawSlider * 9 / 5 + 32 : rawSlider;
    const displayTemp = isCelsius ? rawSlider : rawSlider;
    tempDisplay.textContent = displayTemp;
    humidityDisp.textContent = RH;

    if (sunCheck.checked)  T_F += 10;
    if (windCheck.checked) T_F += (RH > 85 ? 2 : -1);

    let hi_F = calcHeatIndex(T_F, RH);
    let hi_display = isCelsius
      ? Math.round((hi_F - 32) * 5 / 9 * 10) / 10
      : Math.round(hi_F * 10) / 10;

    const risk = getRisk(hi_F);

    hiValueEl.textContent   = hi_display;
    hiUnitLabel.textContent = isCelsius ? '°C' : '°F';
    riskLevelEl.textContent = risk.level;
    riskDescEl.textContent  = risk.desc;
    riskIconEl.textContent  = risk.icon;

    resultPanel.style.setProperty('--risk-color', risk.color);
    riskLevelEl.style.color = risk.color;
    hiValueEl.style.color   = risk.color;

    gaugeNeedle.style.left  = risk.pct + '%';
    gaugePctLabel.textContent = risk.level.toUpperCase();

    const alerts = getAlerts(hi_F);
    [
      ['alert-hydration', 'alert-hydration-text', alerts.hydration],
      ['alert-activity',  'alert-activity-text',  alerts.activity],
      ['alert-vulnerable','alert-vulnerable-text', alerts.vulnerable],
      ['alert-emergency', 'alert-emergency-text',  alerts.emergency],
    ].forEach(([cardId, textId, data]) => {
      document.getElementById(cardId).classList.toggle('active', data.active);
      document.getElementById(textId).textContent = data.text;
    });
  }

  // ─── Event listeners ─────────────────────────────────────────────────────
  tempSlider.addEventListener('input', update);
  humiditySlider.addEventListener('input', update);
  sunCheck.addEventListener('change', update);
  windCheck.addEventListener('change', update);

  // ─── Live clock ──────────────────────────────────────────────────────────
  function tick() {
    document.getElementById('live-time').textContent =
      new Date().toLocaleTimeString('en-IN', { hour12: false });
  }
  tick();
  setInterval(tick, 1000);

  // ─── Init ────────────────────────────────────────────────────────────────
  update();
</script>
</body>
</html>
