# Gamma-fx
Gamma exposure analyzer

<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0">
<title>GEX · Gamma Exposure Monitor</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link href="https://fonts.googleapis.com/css2?family=IBM+Plex+Mono:wght@400;500;600&family=IBM+Plex+Sans:wght@300;400;500;600&display=swap" rel="stylesheet">
<style>
  :root {
    --bg: #0a0c10;
    --bg2: #111318;
    --bg3: #181b22;
    --border: rgba(255,255,255,0.07);
    --border2: rgba(255,255,255,0.12);
    --text: #e8eaf0;
    --muted: #6b7280;
    --dim: #3a3f4b;
    --green: #00d4aa;
    --green2: #00a882;
    --red: #ff5b6e;
    --red2: #cc3848;
    --amber: #f59e0b;
    --blue: #3b82f6;
    --purple: #8b5cf6;
    --gold: #f5c842;
    --mono: 'IBM Plex Mono', monospace;
    --sans: 'IBM Plex Sans', sans-serif;
  }

  * { margin: 0; padding: 0; box-sizing: border-box; }

  body {
    background: var(--bg);
    color: var(--text);
    font-family: var(--sans);
    font-size: 14px;
    min-height: 100vh;
    overflow-x: hidden;
  }

  .header {
    padding: 16px;
    border-bottom: 1px solid var(--border);
    display: flex;
    align-items: center;
    justify-content: space-between;
    position: sticky;
    top: 0;
    z-index: 100;
    background: var(--bg);
  }

  .logo {
    font-family: var(--mono);
    font-size: 13px;
    font-weight: 600;
    letter-spacing: 0.08em;
    color: var(--green);
  }

  .logo span { color: var(--muted); }

  .refresh-btn {
    background: var(--bg3);
    border: 1px solid var(--border2);
    color: var(--text);
    padding: 6px 14px;
    border-radius: 6px;
    font-family: var(--mono);
    font-size: 11px;
    cursor: pointer;
    letter-spacing: 0.05em;
    transition: all 0.15s;
  }
  .refresh-btn:hover { border-color: var(--green); color: var(--green); }
  .refresh-btn:disabled { opacity: 0.4; cursor: not-allowed; }

  .last-update {
    font-family: var(--mono);
    font-size: 10px;
    color: var(--muted);
    text-align: center;
    padding: 6px;
    border-bottom: 1px solid var(--border);
  }

  .section { padding: 0 12px; margin-bottom: 6px; }

  .asset-header {
    display: flex;
    align-items: center;
    gap: 10px;
    padding: 14px 0 8px;
  }

  .asset-dot {
    width: 8px; height: 8px;
    border-radius: 50%;
    flex-shrink: 0;
  }

  .asset-name {
    font-family: var(--mono);
    font-size: 13px;
    font-weight: 600;
    letter-spacing: 0.06em;
  }

  .asset-price {
    font-family: var(--mono);
    font-size: 13px;
    color: var(--muted);
    margin-left: auto;
  }

  .asset-source {
    font-size: 10px;
    color: var(--dim);
    font-family: var(--mono);
  }

  .card {
    background: var(--bg2);
    border: 1px solid var(--border);
    border-radius: 10px;
    padding: 14px;
    margin-bottom: 8px;
  }

  .card-title {
    font-size: 10px;
    font-family: var(--mono);
    color: var(--muted);
    letter-spacing: 0.1em;
    text-transform: uppercase;
    margin-bottom: 10px;
  }

  .metrics-row {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 8px;
  }

  .metric {
    background: var(--bg3);
    border-radius: 7px;
    padding: 10px 12px;
  }

  .metric-label {
    font-size: 10px;
    color: var(--muted);
    font-family: var(--mono);
    letter-spacing: 0.06em;
    margin-bottom: 4px;
  }

  .metric-value {
    font-family: var(--mono);
    font-size: 16px;
    font-weight: 600;
    color: var(--text);
  }

  .metric-sub {
    font-size: 10px;
    color: var(--muted);
    font-family: var(--mono);
    margin-top: 2px;
  }

  .signal-box {
    border-radius: 10px;
    padding: 14px;
    margin-bottom: 8px;
    border: 1px solid transparent;
  }

  .signal-box.bullish {
    background: rgba(0, 212, 170, 0.08);
    border-color: rgba(0, 212, 170, 0.25);
  }

  .signal-box.bearish {
    background: rgba(255, 91, 110, 0.08);
    border-color: rgba(255, 91, 110, 0.25);
  }

  .signal-box.neutral {
    background: rgba(245, 158, 11, 0.07);
    border-color: rgba(245, 158, 11, 0.2);
  }

  .signal-header {
    display: flex;
    align-items: center;
    gap: 10px;
    margin-bottom: 8px;
  }

  .signal-label {
    font-size: 10px;
    font-family: var(--mono);
    color: var(--muted);
    letter-spacing: 0.1em;
  }

  .signal-direction {
    font-family: var(--mono);
    font-size: 18px;
    font-weight: 600;
  }

  .bullish .signal-direction { color: var(--green); }
  .bearish .signal-direction { color: var(--red); }
  .neutral .signal-direction { color: var(--amber); }

  .conviction-bar {
    height: 4px;
    background: var(--bg3);
    border-radius: 2px;
    overflow: hidden;
    margin: 8px 0;
  }

  .conviction-fill {
    height: 100%;
    border-radius: 2px;
    transition: width 0.6s ease;
  }

  .bullish .conviction-fill { background: var(--green); }
  .bearish .conviction-fill { background: var(--red); }
  .neutral .conviction-fill { background: var(--amber); }

  .conviction-pct {
    font-family: var(--mono);
    font-size: 11px;
    color: var(--muted);
    text-align: right;
  }

  .signal-reasons {
    margin-top: 10px;
    display: flex;
    flex-direction: column;
    gap: 5px;
  }

  .reason {
    font-size: 11px;
    color: var(--muted);
    font-family: var(--mono);
    display: flex;
    align-items: flex-start;
    gap: 6px;
    line-height: 1.5;
  }

  .reason-dot {
    width: 4px; height: 4px;
    border-radius: 50%;
    margin-top: 5px;
    flex-shrink: 0;
  }
  .bullish .reason-dot { background: var(--green); }
  .bearish .reason-dot { background: var(--red); }
  .neutral .reason-dot { background: var(--amber); }

  .gex-bar-section { margin-top: 10px; }

  .gex-row {
    display: flex;
    align-items: center;
    gap: 8px;
    margin-bottom: 5px;
  }

  .gex-label {
    font-family: var(--mono);
    font-size: 10px;
    color: var(--muted);
    min-width: 40px;
  }

  .gex-track {
    flex: 1;
    height: 12px;
    background: var(--bg3);
    border-radius: 2px;
    overflow: hidden;
    position: relative;
  }

  .gex-fill-positive {
    height: 100%;
    background: var(--green);
    border-radius: 2px;
    transition: width 0.5s ease;
  }

  .gex-fill-negative {
    height: 100%;
    background: var(--red);
    border-radius: 2px;
    transition: width 0.5s ease;
  }

  .gex-val {
    font-family: var(--mono);
    font-size: 10px;
    color: var(--muted);
    min-width: 56px;
    text-align: right;
  }

  .oi-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 8px; }

  .oi-item {
    background: var(--bg3);
    border-radius: 7px;
    padding: 10px;
  }

  .oi-label {
    font-size: 9px;
    font-family: var(--mono);
    color: var(--dim);
    letter-spacing: 0.08em;
    margin-bottom: 4px;
  }

  .oi-val {
    font-family: var(--mono);
    font-size: 14px;
    font-weight: 600;
  }

  .oi-change {
    font-size: 10px;
    font-family: var(--mono);
    margin-top: 2px;
  }

  .pos { color: var(--green); }
  .neg { color: var(--red); }
  .neu { color: var(--amber); }

  .divider {
    height: 1px;
    background: var(--border);
    margin: 8px 0;
  }

  .loading-state {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    padding: 40px 20px;
    gap: 12px;
  }

  .spinner {
    width: 24px; height: 24px;
    border: 2px solid var(--bg3);
    border-top-color: var(--green);
    border-radius: 50%;
    animation: spin 0.7s linear infinite;
  }

  @keyframes spin { to { transform: rotate(360deg); } }

  .loading-text {
    font-family: var(--mono);
    font-size: 11px;
    color: var(--muted);
    letter-spacing: 0.06em;
  }

  .error-box {
    background: rgba(255,91,110,0.08);
    border: 1px solid rgba(255,91,110,0.2);
    border-radius: 8px;
    padding: 12px;
    font-family: var(--mono);
    font-size: 11px;
    color: var(--red);
    margin: 8px 0;
  }

  .section-divider {
    height: 6px;
    background: var(--bg);
  }

  .footer-note {
    padding: 20px 16px;
    font-size: 10px;
    font-family: var(--mono);
    color: var(--dim);
    line-height: 1.7;
    border-top: 1px solid var(--border);
    margin-top: 10px;
  }

  .pill {
    display: inline-block;
    padding: 2px 8px;
    border-radius: 4px;
    font-family: var(--mono);
    font-size: 10px;
    font-weight: 500;
  }

  .pill-green { background: rgba(0,212,170,0.15); color: var(--green); }
  .pill-red { background: rgba(255,91,110,0.15); color: var(--red); }
  .pill-amber { background: rgba(245,158,11,0.15); color: var(--amber); }
  .pill-purple { background: rgba(139,92,246,0.15); color: var(--purple); }

  .ratio-row {
    display: flex;
    align-items: center;
    gap: 10px;
    margin: 8px 0;
  }

  .ratio-label { font-family: var(--mono); font-size: 11px; color: var(--muted); }

  .pcr-bar {
    flex: 1;
    height: 8px;
    background: var(--bg3);
    border-radius: 4px;
    overflow: hidden;
    display: flex;
  }

  .pcr-put { background: var(--red); height: 100%; }
  .pcr-call { background: var(--green); height: 100%; }

  .asset-container { margin-bottom: 4px; }

  .data-source-badge {
    font-family: var(--mono);
    font-size: 9px;
    color: var(--dim);
    background: var(--bg3);
    border: 1px solid var(--border);
    border-radius: 3px;
    padding: 2px 6px;
    display: inline-block;
  }
</style>
</head>
<body>

<div class="header">
  <div>
    <div class="logo">GEX<span>·</span>MONITOR</div>
    <div style="font-size:9px; color:var(--dim); font-family:var(--mono); margin-top:2px;">GAMMA · OI · SIGNALS</div>
  </div>
  <button class="refresh-btn" id="refreshBtn" onclick="fetchAll()">⟳ REFRESH</button>
</div>

<div class="last-update" id="lastUpdate">— awaiting data —</div>

<div id="mainContent">
  <div class="loading-state">
    <div class="spinner"></div>
    <div class="loading-text">FETCHING LIVE DATA...</div>
    <div class="loading-text" style="color:var(--dim)">DERIBIT · BINANCE</div>
  </div>
</div>

<script>
const BINANCE_BASE = 'https://fapi.binance.com';
const BINANCE_SPOT = 'https://api.binance.com';
const DERIBIT_BASE = 'https://www.deribit.com/api/v2';

const CORS_PROXY = 'https://corsproxy.io/?';

async function fetchJSON(url, useProxy = false) {
  const target = useProxy ? CORS_PROXY + encodeURIComponent(url) : url;
  const res = await fetch(target, { headers: { 'Accept': 'application/json' } });
  if (!res.ok) throw new Error(`HTTP ${res.status}`);
  return res.json();
}

async function getBtcData() {
  const result = { price: null, oi: null, funding: null, gex: null, pcr: null, strikes: [], error: null };
  try {
    const [ticker, oi, funding] = await Promise.all([
      fetchJSON(`${BINANCE_BASE}/fapi/v1/ticker/24hr?symbol=BTCUSDT`),
      fetchJSON(`${BINANCE_BASE}/fapi/v1/openInterest?symbol=BTCUSDT`),
      fetchJSON(`${BINANCE_BASE}/fapi/v1/fundingRate?symbol=BTCUSDT&limit=1`)
    ]);
    result.price = parseFloat(ticker.lastPrice);
    result.oi = parseFloat(oi.openInterest) * result.price;
    result.oiBtc = parseFloat(oi.openInterest);
    result.priceChange = parseFloat(ticker.priceChangePercent);
    result.funding = parseFloat(funding[0]?.fundingRate) * 100;
    result.volume = parseFloat(ticker.quoteVolume);
  } catch(e) { result.error = 'Binance futures: ' + e.message; }

  try {
    const expiries = await fetchJSON(`${DERIBIT_BASE}/public/get_instruments?currency=BTC&kind=option&expired=false`);
    if (expiries?.result) {
      const now = Date.now();
      const nearExpiries = expiries.result
        .filter(i => {
          const exp = i.expiration_timestamp;
          const daysOut = (exp - now) / 86400000;
          return daysOut > 0 && daysOut <= 30;
        })
        .slice(0, 60);

      const bookData = await Promise.all(
        nearExpiries.slice(0, 30).map(inst =>
          fetchJSON(`${DERIBIT_BASE}/public/get_order_book?instrument_name=${inst.instrument_name}&depth=1`)
            .then(r => ({ inst, book: r.result }))
            .catch(() => null)
        )
      );

      let callGex = 0, putGex = 0;
      let callOI = 0, putOI = 0;
      const strikePressure = {};

      bookData.forEach(item => {
        if (!item || !item.book) return;
        const { inst, book } = item;
        const isCall = inst.option_type === 'call';
        const strike = inst.strike;
        const oi = book.open_interest || 0;
        const gamma = book.greeks?.gamma || 0;
        const undPrice = book.underlying_price || result.price || 60000;
        const contractSize = 1;
        const gexContrib = gamma * oi * contractSize * undPrice * undPrice * 0.01;

        if (isCall) {
          callGex += gexContrib;
          callOI += oi;
        } else {
          putGex += gexContrib;
          putOI += oi;
        }

        if (!strikePressure[strike]) strikePressure[strike] = { callOI: 0, putOI: 0, strike };
        if (isCall) strikePressure[strike].callOI += oi;
        else strikePressure[strike].putOI += oi;
      });

      result.gex = { call: callGex, put: putGex, net: callGex - putGex };
      result.pcr = callOI > 0 ? putOI / callOI : null;
      result.callOI = callOI;
      result.putOI = putOI;

      const sortedStrikes = Object.values(strikePressure)
        .filter(s => s.callOI + s.putOI > 0)
        .sort((a, b) => (b.callOI + b.putOI) - (a.callOI + a.putOI))
        .slice(0, 5);
      result.strikes = sortedStrikes;
    }
  } catch(e) {
    result.deribitError = 'Deribit: ' + e.message;
  }

  return result;
}

async function getXauData() {
  const result = { price: null, oi: null, funding: null, error: null };
  try {
    const [ticker, oi, funding] = await Promise.all([
      fetchJSON(`${BINANCE_BASE}/fapi/v1/ticker/24hr?symbol=XAUUSDT`),
      fetchJSON(`${BINANCE_BASE}/fapi/v1/openInterest?symbol=XAUUSDT`),
      fetchJSON(`${BINANCE_BASE}/fapi/v1/fundingRate?symbol=XAUUSDT&limit=1`)
    ]);
    result.price = parseFloat(ticker.lastPrice);
    result.oi = parseFloat(oi.openInterest) * result.price;
    result.oiUnits = parseFloat(oi.openInterest);
    result.priceChange = parseFloat(ticker.priceChangePercent);
    result.funding = parseFloat(funding[0]?.fundingRate) * 100;
    result.volume = parseFloat(ticker.quoteVolume);
    result.highPrice = parseFloat(ticker.highPrice);
    result.lowPrice = parseFloat(ticker.lowPrice);
  } catch(e) {
    result.error = e.message;
    try {
      const spot = await fetchJSON(`${BINANCE_SPOT}/api/v3/ticker/24hr?symbol=XAUUSDT`);
      result.price = parseFloat(spot.lastPrice);
      result.priceChange = parseFloat(spot.priceChangePercent);
      result.volume = parseFloat(spot.quoteVolume);
      result.spotOnly = true;
    } catch(e2) { result.spotError = e2.message; }
  }
  return result;
}

async function getEurData() {
  const result = { price: null, oi: null, funding: null, error: null };
  try {
    const [ticker, oi, funding] = await Promise.all([
      fetchJSON(`${BINANCE_BASE}/fapi/v1/ticker/24hr?symbol=EURUSDT`),
      fetchJSON(`${BINANCE_BASE}/fapi/v1/openInterest?symbol=EURUSDT`),
      fetchJSON(`${BINANCE_BASE}/fapi/v1/fundingRate?symbol=EURUSDT&limit=1`)
    ]);
    result.price = parseFloat(ticker.lastPrice);
    result.oi = parseFloat(oi.openInterest) * result.price;
    result.oiUnits = parseFloat(oi.openInterest);
    result.priceChange = parseFloat(ticker.priceChangePercent);
    result.funding = parseFloat(funding[0]?.fundingRate) * 100;
    result.volume = parseFloat(ticker.quoteVolume);
    result.highPrice = parseFloat(ticker.highPrice);
    result.lowPrice = parseFloat(ticker.lowPrice);
  } catch(e) {
    result.error = e.message;
  }
  return result;
}

function fmtNum(n, dec = 2) {
  if (n == null || isNaN(n)) return '—';
  return n.toFixed(dec);
}

function fmtLarge(n) {
  if (n == null || isNaN(n)) return '—';
  if (n >= 1e9) return '$' + (n / 1e9).toFixed(2) + 'B';
  if (n >= 1e6) return '$' + (n / 1e6).toFixed(2) + 'M';
  if (n >= 1e3) return '$' + (n / 1e3).toFixed(2) + 'K';
  return '$' + n.toFixed(2);
}

function fmtGex(n) {
  if (n == null || isNaN(n)) return '—';
  const abs = Math.abs(n);
  const sign = n >= 0 ? '+' : '-';
  if (abs >= 1e9) return sign + '$' + (abs / 1e9).toFixed(2) + 'B';
  if (abs >= 1e6) return sign + '$' + (abs / 1e6).toFixed(2) + 'M';
  if (abs >= 1e3) return sign + '$' + (abs / 1e3).toFixed(2) + 'K';
  return sign + '$' + abs.toFixed(0);
}

function computeBtcSignal(data) {
  const reasons = [];
  let score = 0;

  if (data.funding != null) {
    if (data.funding > 0.05) { score -= 1; reasons.push('Funding rate elevated (' + fmtNum(data.funding, 4) + '%) — longs paying shorts, possible reversal pressure'); }
    else if (data.funding > 0) { score += 0.5; reasons.push('Funding rate positive but moderate (' + fmtNum(data.funding, 4) + '%) — mild bullish bias'); }
    else if (data.funding < -0.02) { score += 1; reasons.push('Negative funding (' + fmtNum(data.funding, 4) + '%) — shorts paying longs, potential squeeze setup'); }
  }

  if (data.gex) {
    const net = data.gex.net;
    const totalGex = data.gex.call + data.gex.put;
    if (net > 0 && totalGex > 0) {
      const ratio = net / totalGex;
      if (ratio > 0.3) { score += 1.5; reasons.push('Net GEX strongly positive — market makers delta-hedging buying into dips, pins current range'); }
      else { score += 0.5; reasons.push('Net GEX slightly positive — mild dealer support below spot'); }
    } else if (net < 0) {
      score -= 1.5;
      reasons.push('Net GEX negative — dealers hedging by selling, amplifies moves downward');
    }
  }

  if (data.pcr != null) {
    if (data.pcr > 1.2) { score += 1; reasons.push('Put/Call OI ratio ' + fmtNum(data.pcr, 2) + ' — heavy put loading, contrarian bullish signal (fear excessive)'); }
    else if (data.pcr < 0.7) { score -= 1; reasons.push('Put/Call OI ratio ' + fmtNum(data.pcr, 2) + ' — call-heavy, complacency risk, potential reversal'); }
    else { reasons.push('Put/Call OI ratio ' + fmtNum(data.pcr, 2) + ' — balanced, no directional extreme'); }
  }

  if (data.priceChange != null) {
    if (data.priceChange > 3) { score += 0.5; reasons.push('24h momentum +' + fmtNum(data.priceChange, 2) + '% — trending up'); }
    else if (data.priceChange < -3) { score -= 0.5; reasons.push('24h momentum ' + fmtNum(data.priceChange, 2) + '% — trending down'); }
  }

  let direction, conviction;
  if (score > 1.5) { direction = '▲ BULLISH'; conviction = Math.min(95, 50 + score * 15); }
  else if (score > 0.3) { direction = '▲ MILD BULL'; conviction = Math.min(70, 40 + score * 15); }
  else if (score < -1.5) { direction = '▼ BEARISH'; conviction = Math.min(95, 50 + Math.abs(score) * 15); }
  else if (score < -0.3) { direction = '▼ MILD BEAR'; conviction = Math.min(70, 40 + Math.abs(score) * 15); }
  else { direction = '◆ NEUTRAL'; conviction = 35; }

  const cls = direction.includes('BULL') ? 'bullish' : direction.includes('BEAR') ? 'bearish' : 'neutral';
  return { direction, conviction: Math.round(conviction), cls, reasons };
}

function computeSpotSignal(data, name) {
  const reasons = [];
  let score = 0;

  if (data.funding != null && !data.spotOnly) {
    if (data.funding > 0.03) { score -= 1; reasons.push('Futures funding ' + fmtNum(data.funding, 4) + '% — overheated longs'); }
    else if (data.funding < -0.01) { score += 1; reasons.push('Negative funding ' + fmtNum(data.funding, 4) + '% — short squeeze potential'); }
    else { score += 0.3; reasons.push('Funding neutral ' + fmtNum(data.funding, 4) + '% — no directional skew'); }
  }

  if (data.oi != null && data.oi > 0) {
    const oiVol = data.oi / (data.volume || 1);
    if (oiVol > 0.5) { score += 0.5; reasons.push('OI/Volume ratio ' + fmtNum(oiVol, 2) + ' — institutional positioning elevated vs flow'); }
    else { reasons.push('OI/Volume ratio ' + fmtNum(oiVol, 2) + ' — normal positioning depth'); }
  }

  if (data.priceChange != null) {
    if (data.priceChange > 1.5) { score += 0.5; reasons.push('24h move +' + fmtNum(data.priceChange, 2) + '% with rising OI — conviction trend'); }
    else if (data.priceChange < -1.5) { score -= 0.5; reasons.push('24h move ' + fmtNum(data.priceChange, 2) + '% — bearish pressure'); }
    else { reasons.push('24h move ' + fmtNum(data.priceChange, 2) + '% — range bound'); }
  }

  if (data.highPrice && data.lowPrice && data.price) {
    const range = data.highPrice - data.lowPrice;
    const pos = (data.price - data.lowPrice) / range;
    if (pos > 0.75) { score += 0.5; reasons.push('Price at ' + Math.round(pos*100) + '% of daily range — near highs, momentum bias'); }
    else if (pos < 0.25) { score -= 0.3; reasons.push('Price at ' + Math.round(pos*100) + '% of daily range — near lows'); }
  }

  let direction, conviction;
  if (score > 1.2) { direction = '▲ BULLISH'; conviction = Math.min(90, 45 + score * 18); }
  else if (score > 0.3) { direction = '▲ MILD BULL'; conviction = Math.min(65, 38 + score * 15); }
  else if (score < -1.2) { direction = '▼ BEARISH'; conviction = Math.min(90, 45 + Math.abs(score) * 18); }
  else if (score < -0.3) { direction = '▼ MILD BEAR'; conviction = Math.min(65, 38 + Math.abs(score) * 15); }
  else { direction = '◆ NEUTRAL'; conviction = 30; }

  const cls = direction.includes('BULL') ? 'bullish' : direction.includes('BEAR') ? 'bearish' : 'neutral';
  return { direction, conviction: Math.round(conviction), cls, reasons };
}

function renderBtcSection(data) {
  const signal = computeBtcSignal(data);
  const gex = data.gex;
  const maxGex = gex ? Math.max(gex.call, gex.put, 1) : 1;

  return `
  <div class="section asset-container">
    <div class="asset-header">
      <div class="asset-dot" style="background:var(--amber)"></div>
      <div>
        <div class="asset-name">BTC / USDT</div>
        <div class="asset-source">
          <span class="data-source-badge">BINANCE FUTURES</span>
          <span class="data-source-badge" style="margin-left:4px">DERIBIT OPTIONS</span>
        </div>
      </div>
      <div class="asset-price">${data.price ? '$' + Math.round(data.price).toLocaleString() : '—'}
        ${data.priceChange != null ? `<span class="${data.priceChange >= 0 ? 'pos' : 'neg'}">${data.priceChange >= 0 ? '+' : ''}${fmtNum(data.priceChange,2)}%</span>` : ''}
      </div>
    </div>

    ${data.error ? `<div class="error-box">⚠ Futures: ${data.error}</div>` : ''}
    ${data.deribitError ? `<div class="error-box">⚠ Options: ${data.deribitError}</div>` : ''}

    <div class="signal-box ${signal.cls}">
      <div class="signal-header">
        <div>
          <div class="signal-label">GEX·OI SIGNAL</div>
          <div class="signal-direction">${signal.direction}</div>
        </div>
        <div style="margin-left:auto; text-align:right">
          <div class="signal-label">CONVICTION</div>
          <div class="signal-direction">${signal.conviction}%</div>
        </div>
      </div>
      <div class="conviction-bar">
        <div class="conviction-fill" style="width:${signal.conviction}%"></div>
      </div>
      <div class="conviction-pct">${signal.conviction}% conviction</div>
      <div class="signal-reasons">
        ${signal.reasons.map(r => `<div class="reason"><div class="reason-dot"></div>${r}</div>`).join('')}
      </div>
    </div>

    ${gex ? `
    <div class="card">
      <div class="card-title">GAMMA EXPOSURE (DERIBIT OPTIONS)</div>
      <div class="gex-bar-section">
        <div class="gex-row">
          <div class="gex-label">CALL</div>
          <div class="gex-track"><div class="gex-fill-positive" style="width:${Math.min(100,(gex.call/maxGex)*100).toFixed(1)}%"></div></div>
          <div class="gex-val pos">${fmtGex(gex.call)}</div>
        </div>
        <div class="gex-row">
          <div class="gex-label">PUT</div>
          <div class="gex-track"><div class="gex-fill-negative" style="width:${Math.min(100,(gex.put/maxGex)*100).toFixed(1)}%"></div></div>
          <div class="gex-val neg">${fmtGex(-gex.put)}</div>
        </div>
        <div class="divider"></div>
        <div class="gex-row">
          <div class="gex-label">NET</div>
          <div class="gex-track">
            <div class="${gex.net >= 0 ? 'gex-fill-positive' : 'gex-fill-negative'}" style="width:${Math.min(100,(Math.abs(gex.net)/maxGex)*100).toFixed(1)}%"></div>
          </div>
          <div class="gex-val ${gex.net >= 0 ? 'pos' : 'neg'}">${fmtGex(gex.net)}</div>
        </div>
      </div>
      ${data.pcr != null ? `
      <div class="divider"></div>
      <div class="card-title" style="margin-top:8px; margin-bottom:6px">PUT / CALL OI RATIO</div>
      <div class="ratio-row">
        <div class="ratio-label">P/C: ${fmtNum(data.pcr,3)}</div>
        <div class="pcr-bar">
          <div class="pcr-put" style="width:${Math.min(95,data.pcr/(data.pcr+1)*100).toFixed(1)}%"></div>
          <div class="pcr-call" style="flex:1"></div>
        </div>
        <span class="pill ${data.pcr > 1.1 ? 'pill-red' : data.pcr < 0.8 ? 'pill-green' : 'pill-amber'}">${data.pcr > 1.1 ? 'BEARISH' : data.pcr < 0.8 ? 'CALLS DOM.' : 'BALANCED'}</span>
      </div>
      <div style="display:flex;gap:6px;margin-top:6px">
        <div class="metric" style="flex:1">
          <div class="metric-label">CALL OI</div>
          <div class="metric-value" style="font-size:13px">${data.callOI ? Math.round(data.callOI).toLocaleString() : '—'}</div>
          <div class="metric-sub">contracts</div>
        </div>
        <div class="metric" style="flex:1">
          <div class="metric-label">PUT OI</div>
          <div class="metric-value" style="font-size:13px">${data.putOI ? Math.round(data.putOI).toLocaleString() : '—'}</div>
          <div class="metric-sub">contracts</div>
        </div>
      </div>
      ` : ''}
    </div>
    ` : ''}

    <div class="card">
      <div class="card-title">FUTURES DATA · BINANCE</div>
      <div class="metrics-row">
        <div class="metric">
          <div class="metric-label">OPEN INTEREST</div>
          <div class="metric-value" style="font-size:14px">${fmtLarge(data.oi)}</div>
          <div class="metric-sub">${data.oiBtc ? data.oiBtc.toFixed(1) + ' BTC' : ''}</div>
        </div>
        <div class="metric">
          <div class="metric-label">FUNDING RATE</div>
          <div class="metric-value ${data.funding > 0 ? 'pos' : data.funding < 0 ? 'neg' : ''}" style="font-size:14px">
            ${data.funding != null ? (data.funding > 0 ? '+' : '') + fmtNum(data.funding,4) + '%' : '—'}
          </div>
          <div class="metric-sub">8h rate</div>
        </div>
        <div class="metric">
          <div class="metric-label">24H VOLUME</div>
          <div class="metric-value" style="font-size:14px">${fmtLarge(data.volume)}</div>
          <div class="metric-sub">USDT</div>
        </div>
        <div class="metric">
          <div class="metric-label">24H CHANGE</div>
          <div class="metric-value ${data.priceChange >= 0 ? 'pos' : 'neg'}" style="font-size:14px">
            ${data.priceChange != null ? (data.priceChange >= 0 ? '+' : '') + fmtNum(data.priceChange,2) + '%' : '—'}
          </div>
          <div class="metric-sub">vs prev close</div>
        </div>
      </div>
    </div>

    ${data.strikes && data.strikes.length > 0 ? `
    <div class="card">
      <div class="card-title">TOP OI STRIKE CLUSTERS</div>
      ${data.strikes.map(s => `
      <div style="display:flex;align-items:center;gap:8px;margin-bottom:6px">
        <div style="font-family:var(--mono);font-size:12px;font-weight:600;min-width:72px">$${Number(s.strike).toLocaleString()}</div>
        <div style="flex:1">
          <div style="height:4px;background:rgba(0,212,170,0.15);border-radius:2px;margin-bottom:2px">
            <div style="height:4px;background:var(--green);border-radius:2px;width:${Math.min(100,(s.callOI/(s.callOI+s.putOI+0.001))*100).toFixed(0)}%"></div>
          </div>
          <div style="height:4px;background:rgba(255,91,110,0.15);border-radius:2px">
            <div style="height:4px;background:var(--red);border-radius:2px;width:${Math.min(100,(s.putOI/(s.callOI+s.putOI+0.001))*100).toFixed(0)}%"></div>
          </div>
        </div>
        <div style="font-family:var(--mono);font-size:9px;color:var(--muted);min-width:50px;text-align:right">
          <span class="pos">C:${Math.round(s.callOI)}</span><br><span class="neg">P:${Math.round(s.putOI)}</span>
        </div>
      </div>
      `).join('')}
    </div>
    ` : ''}
  </div>`;
}

function renderSpotSection(data, name, symbol, dotColor, note) {
  const signal = computeSpotSignal(data, name);
  return `
  <div class="section asset-container">
    <div class="asset-header">
      <div class="asset-dot" style="background:${dotColor}"></div>
      <div>
        <div class="asset-name">${name}</div>
        <div class="asset-source">
          <span class="data-source-badge">${data.spotOnly ? 'BINANCE SPOT' : 'BINANCE FUTURES'}</span>
        </div>
      </div>
      <div class="asset-price">${data.price ? (symbol === 'EUR' ? data.price.toFixed(4) : '$' + data.price.toFixed(2)) : '—'}
        ${data.priceChange != null ? `<span class="${data.priceChange >= 0 ? 'pos' : 'neg'}">${data.priceChange >= 0 ? '+' : ''}${fmtNum(data.priceChange,2)}%</span>` : ''}
      </div>
    </div>

    ${data.error && data.spotError ? `<div class="error-box">⚠ ${data.error}</div>` : ''}
    ${note ? `<div style="font-family:var(--mono);font-size:10px;color:var(--dim);margin-bottom:8px;line-height:1.5">${note}</div>` : ''}

    <div class="signal-box ${signal.cls}">
      <div class="signal-header">
        <div>
          <div class="signal-label">OI + FUNDING SIGNAL</div>
          <div class="signal-direction">${signal.direction}</div>
        </div>
        <div style="margin-left:auto; text-align:right">
          <div class="signal-label">CONVICTION</div>
          <div class="signal-direction">${signal.conviction}%</div>
        </div>
      </div>
      <div class="conviction-bar">
        <div class="conviction-fill" style="width:${signal.conviction}%"></div>
      </div>
      <div class="conviction-pct">${signal.conviction}% conviction</div>
      <div class="signal-reasons">
        ${signal.reasons.map(r => `<div class="reason"><div class="reason-dot"></div>${r}</div>`).join('')}
      </div>
    </div>

    <div class="card">
      <div class="card-title">MARKET METRICS</div>
      <div class="metrics-row">
        <div class="metric">
          <div class="metric-label">OPEN INTEREST</div>
          <div class="metric-value" style="font-size:13px">${data.oi ? fmtLarge(data.oi) : '—'}</div>
          <div class="metric-sub">${data.oiUnits ? data.oiUnits.toFixed(2) + ' ' + (symbol === 'EUR' ? 'EUR' : 'XAU') : ''}</div>
        </div>
        <div class="metric">
          <div class="metric-label">FUNDING</div>
          <div class="metric-value ${data.funding > 0 ? 'pos' : data.funding < 0 ? 'neg' : ''}" style="font-size:13px">
            ${data.funding != null ? (data.funding > 0 ? '+' : '') + fmtNum(data.funding,4) + '%' : data.spotOnly ? 'N/A' : '—'}
          </div>
          <div class="metric-sub">8h rate</div>
        </div>
        <div class="metric">
          <div class="metric-label">VOLUME 24H</div>
          <div class="metric-value" style="font-size:13px">${data.volume ? fmtLarge(data.volume) : '—'}</div>
          <div class="metric-sub">USDT notional</div>
        </div>
        <div class="metric">
          <div class="metric-label">DAY RANGE</div>
          <div class="metric-value" style="font-size:11px; line-height:1.8">
            ${data.highPrice && data.lowPrice ? (symbol === 'EUR' ? data.lowPrice.toFixed(4) + '<br>' + data.highPrice.toFixed(4) : data.lowPrice.toFixed(2) + '<br>' + data.highPrice.toFixed(2)) : '—'}
          </div>
        </div>
      </div>
    </div>
  </div>`;
}

async function fetchAll() {
  const btn = document.getElementById('refreshBtn');
  btn.disabled = true;
  btn.textContent = '⟳ LOADING...';

  document.getElementById('mainContent').innerHTML = `
    <div class="loading-state">
      <div class="spinner"></div>
      <div class="loading-text">FETCHING LIVE DATA...</div>
      <div class="loading-text" style="color:var(--dim);margin-top:4px">BINANCE · DERIBIT PUBLIC APIs</div>
    </div>`;

  try {
    const [btc, xau, eur] = await Promise.all([getBtcData(), getXauData(), getEurData()]);

    const html = `
      ${renderBtcSection(btc)}
      <div class="section-divider"></div>
      ${renderSpotSection(xau, 'XAU / USDT · TOKENIZED GOLD', 'XAU', 'var(--gold)',
        xau.spotOnly ? '⚠ Futures data unavailable — showing spot price. OI/Funding N/A.' : null)}
      <div class="section-divider"></div>
      ${renderSpotSection(eur, 'EUR / USDT', 'EUR', 'var(--blue)',
        eur.error ? '⚠ EURUSDT perpetual: ' + eur.error : null)}
      <div class="footer-note">
        DATA SOURCES: Binance Public Futures API (fapi.binance.com) · Deribit Public API (deribit.com/api/v2)<br>
        GEX = Σ(gamma × OI × spot² × 0.01) per option contract. Real Deribit greeks.<br>
        PCR = Put OI ÷ Call OI across near-dated options (≤30 days).<br>
        No simulations. No hallucinated data. Signals = pure OI + GEX + funding algebra.<br>
        NOT financial advice. Use as one input among many.
      </div>`;

    document.getElementById('mainContent').innerHTML = html;
    document.getElementById('lastUpdate').textContent = 'UPDATED: ' + new Date().toLocaleTimeString() + ' · LIVE DATA';
  } catch(e) {
    document.getElementById('mainContent').innerHTML = `<div class="section"><div class="error-box">Fatal error: ${e.message}</div></div>`;
  }

  btn.disabled = false;
  btn.textContent = '⟳ REFRESH';
}

fetchAll();
setInterval(fetchAll, 60000);
</script>
</body>
</html>
