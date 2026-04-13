# yeet123-design.github.io
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Budget Tracker</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link href="https://fonts.googleapis.com/css2?family=DM+Sans:ital,opsz,wght@0,9..40,300;0,9..40,400;0,9..40,500;1,9..40,300&family=DM+Mono:wght@400;500&display=swap" rel="stylesheet">
<script src="https://cdnjs.cloudflare.com/ajax/libs/Chart.js/4.4.1/chart.umd.js"></script>
<style>
  :root {
    --bg: #f6f5f1;
    --surface: #ffffff;
    --surface2: #efede7;
    --border: rgba(0,0,0,0.07);
    --border-md: rgba(0,0,0,0.12);
    --border-strong: rgba(0,0,0,0.18);
    --text: #1c1b18;
    --text-2: #6b6a63;
    --text-3: #a0998e;
    --accent: #2d27a0;
    --accent-light: #eeedfc;
    --accent-mid: #7f77dd;
    --red: #b83030;
    --red-light: #fdf0ee;
    --green: #1a6644;
    --green-light: #e8f5ee;
    --blue: #185fa5;
    --blue-light: #e6f1fb;
    --radius: 14px;
    --radius-sm: 8px;
    --font: 'DM Sans', sans-serif;
    --mono: 'DM Mono', monospace;
    --shadow-sm: 0 1px 3px rgba(0,0,0,0.05);
    --shadow: 0 2px 8px rgba(0,0,0,0.07);
    --shadow-md: 0 4px 20px rgba(0,0,0,0.09);
  }

  @media (prefers-color-scheme: dark) {
    :root {
      --bg: #131210;
      --surface: #1e1c19;
      --surface2: #272420;
      --border: rgba(255,255,255,0.07);
      --border-md: rgba(255,255,255,0.12);
      --border-strong: rgba(255,255,255,0.18);
      --text: #f0ede6;
      --text-2: #9e9a90;
      --text-3: #6b6860;
      --accent: #9d98f5;
      --accent-light: #1e1d2e;
      --accent-mid: #7f77dd;
      --red: #e07068;
      --red-light: #2a1c1a;
      --green: #52c48a;
      --green-light: #162a1f;
      --blue: #6ab0e8;
      --blue-light: #0c2035;
    }
  }

  *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }

  html { font-size: 15px; scroll-behavior: smooth; }

  body {
    font-family: var(--font);
    background: var(--bg);
    color: var(--text);
    line-height: 1.5;
    min-height: 100vh;
    -webkit-font-smoothing: antialiased;
  }

  /* ── Page ── */
  .page { max-width: 1060px; margin: 0 auto; padding: 2rem 1.5rem 5rem; }

  /* ── Topbar ── */
  .topbar {
    display: flex;
    align-items: center;
    justify-content: space-between;
    margin-bottom: 2rem;
    flex-wrap: wrap;
    gap: 1rem;
  }
  .wordmark {
    font-size: 20px;
    font-weight: 500;
    letter-spacing: -0.4px;
    color: var(--text);
  }
  .wordmark em {
    font-style: normal;
    color: var(--accent);
  }
  .month-selector {
    display: flex;
    align-items: center;
    gap: 8px;
    font-size: 13px;
    color: var(--text-2);
  }
  .month-selector select {
    height: 34px;
    border: 1px solid var(--border-md);
    border-radius: var(--radius-sm);
    padding: 0 10px;
    font: 13px/1 var(--font);
    background: var(--surface);
    color: var(--text);
    outline: none;
    cursor: pointer;
    transition: border-color 0.15s;
  }
  .month-selector select:focus { border-color: var(--accent); }

  /* ── Delta bar ── */
  .delta-bar {
    display: grid;
    grid-template-columns: 1fr 1fr 1fr;
    gap: 10px;
    margin-bottom: 1.25rem;
  }
  .delta-card {
    border-radius: var(--radius);
    padding: 1.1rem 1.25rem;
    border: 1px solid var(--border);
    background: var(--surface);
    box-shadow: var(--shadow-sm);
    transition: box-shadow 0.2s;
  }
  .delta-card:hover { box-shadow: var(--shadow); }
  .delta-card .lbl {
    font-size: 11px;
    text-transform: uppercase;
    letter-spacing: 0.07em;
    margin-bottom: 7px;
    font-weight: 500;
  }
  .delta-card .val { font: 500 22px/1 var(--mono); }
  .delta-card .sub { font-size: 11px; margin-top: 5px; }
  .dc-income { background: var(--green-light); border-color: rgba(26,102,68,0.2); }
  .dc-income .lbl, .dc-income .val, .dc-income .sub { color: var(--green); }
  .dc-expense { background: var(--red-light); border-color: rgba(184,48,48,0.2); }
  .dc-expense .lbl, .dc-expense .val, .dc-expense .sub { color: var(--red); }
  .dc-positive { background: var(--green-light); border-color: rgba(26,102,68,0.2); }
  .dc-positive .lbl, .dc-positive .val, .dc-positive .sub { color: var(--green); }
  .dc-negative { background: var(--red-light); border-color: rgba(184,48,48,0.2); }
  .dc-negative .lbl, .dc-negative .val, .dc-negative .sub { color: var(--red); }
  .dc-zero { background: var(--surface2); }
  .dc-zero .lbl, .dc-zero .val { color: var(--text-2); }
  .dc-zero .sub { color: var(--text-3); }

  /* ── Stats row ── */
  .stats-row {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(140px, 1fr));
    gap: 10px;
    margin-bottom: 1.5rem;
  }
  .stat {
    background: var(--surface2);
    border-radius: var(--radius-sm);
    padding: 0.9rem 1.1rem;
  }
  .stat .lbl { font-size: 11px; text-transform: uppercase; letter-spacing: 0.07em; color: var(--text-3); margin-bottom: 6px; font-weight: 500; }
  .stat .val { font: 500 20px/1 var(--mono); }
  .stat .val.red { color: var(--red); }
  .stat .val.blue { color: var(--blue); }
  .stat .val.purple { color: var(--accent); }

  /* ── Add form ── */
  .add-form {
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: var(--radius);
    padding: 1.1rem 1.25rem;
    margin-bottom: 1.5rem;
    box-shadow: var(--shadow-sm);
  }
  .form-tabs { display: flex; gap: 6px; margin-bottom: 1rem; }
  .ftab {
    padding: 5px 14px;
    border-radius: 100px;
    border: 1px solid var(--border-md);
    font: 12px/1 var(--font);
    cursor: pointer;
    background: transparent;
    color: var(--text-2);
    transition: all 0.15s;
    font-weight: 500;
  }
  .ftab:hover { background: var(--surface2); color: var(--text); }
  .ftab.active { background: var(--accent); color: #fff; border-color: var(--accent); }
  .ftab.income-tab.active { background: var(--green); border-color: var(--green); }

  .form-row { display: flex; gap: 8px; flex-wrap: wrap; align-items: flex-end; }
  .field { display: flex; flex-direction: column; gap: 5px; }
  .field label { font-size: 11px; text-transform: uppercase; letter-spacing: 0.06em; color: var(--text-3); font-weight: 500; }
  .field input, .field select {
    height: 38px;
    border: 1px solid var(--border-md);
    border-radius: var(--radius-sm);
    padding: 0 11px;
    font: 14px var(--font);
    background: var(--surface);
    color: var(--text);
    outline: none;
    transition: border-color 0.15s, box-shadow 0.15s;
  }
  .field input:focus, .field select:focus {
    border-color: var(--accent);
    box-shadow: 0 0 0 3px rgba(45,39,160,0.1);
  }
  .field.fdesc { flex: 2; min-width: 150px; position: relative; }
  .field.famt { width: 115px; }
  .field.fdate { width: 145px; }
  .field.fcat { flex: 1; min-width: 165px; }
  .field.fpers { width: 95px; }

  .add-btn {
    height: 38px;
    padding: 0 20px;
    background: var(--accent);
    color: #fff;
    border: none;
    border-radius: var(--radius-sm);
    font: 14px var(--font);
    font-weight: 500;
    cursor: pointer;
    white-space: nowrap;
    transition: background 0.15s, transform 0.1s;
    letter-spacing: 0.01em;
  }
  .add-btn:hover { background: #231e88; }
  .add-btn:active { transform: scale(0.98); }
  .add-btn.income-btn { background: var(--green); }
  .add-btn.income-btn:hover { background: #145234; }

  /* ── Suggestion dropdown ── */
  .suggestion {
    position: absolute;
    top: 100%;
    left: 0;
    right: 0;
    background: var(--surface);
    border: 1px solid var(--border-md);
    border-radius: var(--radius-sm);
    z-index: 100;
    margin-top: 4px;
    overflow: hidden;
    box-shadow: var(--shadow-md);
  }
  .sug-item {
    padding: 9px 12px;
    font-size: 13px;
    cursor: pointer;
    display: flex;
    align-items: center;
    justify-content: space-between;
    gap: 10px;
    transition: background 0.1s;
  }
  .sug-item:hover { background: var(--surface2); }
  .sug-name { font-weight: 500; font-size: 13px; }
  .sug-meta { font-size: 11px; color: var(--text-3); margin-top: 2px; }
  .sug-badge {
    display: inline-block;
    padding: 2px 7px;
    border-radius: 5px;
    font-size: 11px;
    font-weight: 500;
    white-space: nowrap;
  }

  /* ── Charts row ── */
  .charts-row {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 12px;
    margin-bottom: 1.25rem;
  }
  .card {
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: var(--radius);
    padding: 1.1rem 1.25rem;
    box-shadow: var(--shadow-sm);
  }
  .card-title {
    font-size: 11px;
    font-weight: 500;
    text-transform: uppercase;
    letter-spacing: 0.07em;
    color: var(--text-3);
    margin-bottom: 0.9rem;
  }
  .legend {
    display: flex;
    flex-wrap: wrap;
    gap: 8px;
    margin-bottom: 10px;
    font-size: 11px;
    color: var(--text-2);
  }
  .legend span { display: flex; align-items: center; gap: 5px; }
  .legend .sq { width: 9px; height: 9px; border-radius: 2px; flex-shrink: 0; }

  /* ── Fixed costs ── */
  .fixed-card {
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: var(--radius);
    padding: 1.1rem 1.25rem;
    margin-bottom: 1.25rem;
    box-shadow: var(--shadow-sm);
  }
  .fixed-header {
    display: flex;
    align-items: center;
    justify-content: space-between;
    margin-bottom: 1rem;
  }
  .fixed-header .card-title { margin-bottom: 0; }
  .fixed-total-badge {
    font-size: 12px;
    font-weight: 500;
    color: var(--blue);
    background: var(--blue-light);
    padding: 3px 10px;
    border-radius: 6px;
    font-family: var(--mono);
  }
  .fixed-grid {
    display: grid;
    grid-template-columns: repeat(4, 1fr);
    gap: 12px;
  }
  .fixed-item { display: flex; flex-direction: column; gap: 6px; }
  .fixed-label {
    font-size: 11px;
    font-weight: 500;
    text-transform: uppercase;
    letter-spacing: 0.06em;
    color: var(--text-3);
    display: flex;
    align-items: center;
    gap: 5px;
  }
  .fixed-input {
    height: 40px;
    border: 1px solid var(--border-md);
    border-radius: var(--radius-sm);
    padding: 0 12px;
    font: 500 15px var(--mono);
    background: var(--surface);
    color: var(--text);
    outline: none;
    width: 100%;
    transition: all 0.15s;
  }
  .fixed-input::placeholder { font-weight: 400; color: var(--text-3); font-size: 13px; font-family: var(--font); }
  .fixed-input:focus { border-color: var(--blue); box-shadow: 0 0 0 3px rgba(24,95,165,0.1); }
  .fixed-input.filled { border-color: rgba(24,95,165,0.4); color: var(--blue); background: var(--blue-light); }

  /* ── Cat list ── */
  .cat-list { display: flex; flex-direction: column; gap: 2px; }
  .cat-row {
    display: flex;
    align-items: center;
    padding: 7px 8px;
    border-radius: var(--radius-sm);
    cursor: pointer;
    transition: background 0.12s;
    gap: 8px;
  }
  .cat-row:hover, .cat-row.active { background: var(--surface2); }
  .cat-dot { width: 10px; height: 10px; border-radius: 3px; flex-shrink: 0; }
  .cat-name { font-size: 13px; flex: 1; }
  .cat-amt { font-size: 13px; font-weight: 500; font-family: var(--mono); }
  .cat-pct { font-size: 11px; color: var(--text-3); min-width: 32px; text-align: right; font-family: var(--mono); }

  /* ── Transaction tables ── */
  .section-label {
    font-size: 11px;
    font-weight: 500;
    text-transform: uppercase;
    letter-spacing: 0.07em;
    color: var(--text-3);
    margin-bottom: 0.6rem;
    margin-top: 1.25rem;
  }
  .tx-card {
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: var(--radius);
    overflow: hidden;
    margin-bottom: 1.5rem;
    box-shadow: var(--shadow-sm);
  }
  .col-expense { grid-template-columns: 82px 1fr 92px 118px 58px 76px; }
  .col-income  { grid-template-columns: 82px 1fr 92px 58px 76px; }
  .tx-head {
    display: grid;
    gap: 8px;
    padding: 8px 14px;
    font-size: 10px;
    font-weight: 500;
    text-transform: uppercase;
    letter-spacing: 0.07em;
    color: var(--text-3);
    border-bottom: 1px solid var(--border);
    background: var(--surface2);
  }
  .tx-row {
    display: grid;
    gap: 8px;
    padding: 10px 14px;
    font-size: 13px;
    align-items: center;
    border-bottom: 1px solid var(--border);
    transition: background 0.1s;
  }
  .tx-row:last-child { border-bottom: none; }
  .tx-row:hover { background: var(--surface2); }
  .tx-row.editing { background: var(--surface2); }
  .section-sep {
    font-size: 10px;
    font-weight: 500;
    padding: 5px 14px 3px;
    text-transform: uppercase;
    letter-spacing: 0.07em;
  }
  .date-cell { font-size: 12px; color: var(--text-3); font-family: var(--mono); white-space: nowrap; }
  .desc-cell { overflow: hidden; text-overflow: ellipsis; white-space: nowrap; }
  .amt-cell { font-weight: 500; font-family: var(--mono); }

  .badge {
    display: inline-block;
    padding: 2px 7px;
    border-radius: 5px;
    font-size: 11px;
    font-weight: 500;
    white-space: nowrap;
  }
  .action-btns { display: flex; gap: 4px; }
  .btn-sm {
    height: 26px;
    padding: 0 9px;
    font-size: 11px;
    font-weight: 500;
    border: 1px solid var(--border-md);
    border-radius: 6px;
    background: transparent;
    cursor: pointer;
    color: var(--text-2);
    transition: all 0.12s;
    font-family: var(--font);
  }
  .btn-sm:hover { background: var(--surface2); color: var(--text); }
  .btn-del { color: var(--red); border-color: rgba(184,48,48,0.25); }
  .btn-del:hover { background: var(--red-light); }
  .btn-save { color: var(--green); border-color: rgba(26,102,68,0.3); font-weight: 600; }
  .btn-save:hover { background: var(--green-light); }

  .edit-input {
    height: 28px;
    border: 1px solid var(--border-md);
    border-radius: 6px;
    padding: 0 7px;
    font: 12px var(--font);
    background: var(--surface);
    color: var(--text);
    width: 100%;
    outline: none;
    transition: border-color 0.15s;
  }
  .edit-input:focus { border-color: var(--accent); }
  .edit-select {
    height: 28px;
    border: 1px solid var(--border-md);
    border-radius: 6px;
    padding: 0 6px;
    font: 12px var(--font);
    background: var(--surface);
    color: var(--text);
    width: 100%;
    outline: none;
  }

  .empty {
    text-align: center;
    padding: 2.5rem;
    color: var(--text-3);
    font-size: 13px;
  }

  /* ── Animations ── */
  @keyframes fadeUp {
    from { opacity: 0; transform: translateY(10px); }
    to   { opacity: 1; transform: translateY(0); }
  }
  .delta-bar, .stats-row, .add-form, .charts-row, .fixed-card, .tx-card {
    animation: fadeUp 0.35s ease both;
  }
  .delta-bar   { animation-delay: 0.05s; }
  .stats-row   { animation-delay: 0.10s; }
  .add-form    { animation-delay: 0.15s; }
  .charts-row  { animation-delay: 0.20s; }
  .fixed-card  { animation-delay: 0.25s; }

  /* ── Responsive ── */
  @media (max-width: 700px) {
    .charts-row     { grid-template-columns: 1fr; }
    .delta-bar      { grid-template-columns: 1fr; }
    .fixed-grid     { grid-template-columns: 1fr 1fr; }
    .col-expense    { grid-template-columns: 70px 1fr 80px 80px 50px; }
    .col-income     { grid-template-columns: 70px 1fr 80px 50px; }
    .field.fcat     { min-width: 130px; }
  }
  @media (max-width: 480px) {
    .page           { padding: 1rem 1rem 4rem; }
    .fixed-grid     { grid-template-columns: 1fr; }
  }
</style>
</head>
<body>
<div class="page">

  <div class="topbar">
    <div class="wordmark">budget<em>.</em></div>
    <div class="month-selector">
      <span>Viewing</span>
      <select id="viewMonthSel" onchange="onViewMonthChange()"></select>
    </div>
  </div>

  <div class="delta-bar" id="deltaBar"></div>
  <div class="stats-row" id="statsRow"></div>

  <div class="add-form">
    <div class="form-tabs">
      <button class="ftab active" id="tabExpense" onclick="setFormMode('expense')">Expense</button>
      <button class="ftab income-tab" id="tabIncome" onclick="setFormMode('income')">Income</button>
    </div>
    <div class="form-row" id="formRow"></div>
  </div>

  <div class="charts-row">
    <div class="card">
      <div class="card-title">Spending by category</div>
      <div class="legend" id="pieLeg"></div>
      <div style="position:relative;height:210px">
        <canvas id="pieChart" role="img" aria-label="Doughnut chart of spending by category"></canvas>
      </div>
    </div>
    <div class="card">
      <div class="card-title">Colin vs Monica</div>
      <div class="legend" id="pie2Leg"></div>
      <div style="position:relative;height:210px">
        <canvas id="pie2Chart" role="img" aria-label="Pie chart split between Colin and Monica"></canvas>
      </div>
    </div>
  </div>

  <div class="fixed-card" id="fixedCard"></div>

  <div class="card" style="margin-bottom:1.25rem;">
    <div class="card-title">Categories</div>
    <div class="cat-list" id="catList"></div>
  </div>

  <div class="section-label">Income</div>
  <div class="tx-card" id="incomeSection"></div>

  <div class="section-label">Expenses</div>
  <div class="tx-card"><div id="txList"></div></div>

</div>

<script>
const CATS = [
  { id:'groceries',   label:'Groceries',         colors:['#EAF3DE','#639922','#27500A'] },
  { id:'restaurants', label:'Restaurants',        colors:['#FAEEDA','#BA7517','#633806'] },
  { id:'rent',        label:'Rent & fixed',       colors:['#E6F1FB','#378ADD','#0C447C'] },
  { id:'transport',   label:'Transport',          colors:['#F1EFE8','#888780','#444441'] },
  { id:'outings',     label:'Outings & culture',  colors:['#EEEDFE','#7F77DD','#3C3489'] },
  { id:'appearance',  label:'Appearance',         colors:['#FBEAF0','#D4537E','#72243E'] },
  { id:'travel',      label:'Travel',             colors:['#FAECE7','#D85A30','#712B13'] },
  { id:'other',       label:'Other',              colors:['#F1EFE8','#5F5E5A','#444441'] },
];

const FIXED_FIELDS = [
  { id:'rent',        label:'Rent',              icon:'🏠', placeholder:'e.g. 980,00' },
  { id:'electricity', label:'Electricity',       icon:'⚡', placeholder:'e.g. 42,00'  },
  { id:'water',       label:'Water',             icon:'💧', placeholder:'e.g. 27,00'  },
  { id:'internet',    label:'Internet / Phone',  icon:'📡', placeholder:'e.g. 39,00'  },
];

const MONTHS = ['January','February','March','April','May','June','July','August','September','October','November','December'];

/* ── Data ── */
let data = JSON.parse(localStorage.getItem('budgetApp_v4') || '{"txs":[],"income":[]}');
if (!data.income) data.income = [];
if (!data.fixed)  data.fixed  = {};

(function migrate() {
  [...data.txs, ...data.income].forEach(t => {
    if (t.date && (t.month === undefined || t.year === undefined)) {
      const d = new Date(t.date);
      t.month = d.getMonth();
      t.year  = d.getFullYear();
    }
  });
})();

const now = new Date();
let viewMonth = now.getMonth();
let viewYear  = now.getFullYear();
let pieChart  = null;
let pie2Chart = null;
let activeCat = null;
let formMode  = 'expense';
let editingId   = null;
let editingType = null;

/* ── Helpers ── */
function saveData() { localStorage.setItem('budgetApp_v4', JSON.stringify(data)); }

function parseAmt(s) {
  s = String(s).trim();
  const dots   = (s.match(/\./g) || []).length;
  const commas = (s.match(/,/g)  || []).length;
  if (commas === 1 && dots === 0)  s = s.replace(',', '.');
  else if (dots >= 1 && commas === 1) s = s.replace(/\./g, '').replace(',', '.');
  else if (commas >= 1 && dots === 1) s = s.replace(/,/g, '');
  return Math.abs(parseFloat(s) || 0);
}

function fmt(n) {
  return n.toLocaleString('de-DE', { minimumFractionDigits: 2, maximumFractionDigits: 2 });
}

function todayStr() {
  const d = new Date();
  return `${d.getFullYear()}-${String(d.getMonth()+1).padStart(2,'0')}-${String(d.getDate()).padStart(2,'0')}`;
}

function fmtDate(s) {
  if (!s) return '—';
  const [y, m, d] = s.split('-');
  return `${d}/${m}`;
}

function dateToMY(ds) {
  if (!ds) return { month: now.getMonth(), year: now.getFullYear() };
  const d = new Date(ds);
  return { month: d.getMonth(), year: d.getFullYear() };
}

function mKey(mo, yr) { return `${yr}-${String(mo + 1).padStart(2, '0')}`; }

function getViewTxs()    { return data.txs.filter(t => t.month === viewMonth && t.year === viewYear); }
function getViewIncome() { return data.income.filter(t => t.month === viewMonth && t.year === viewYear); }
function getFixed()      { return data.fixed[mKey(viewMonth, viewYear)] || {}; }
function fixedTotal()    { return Object.values(getFixed()).reduce((s, v) => s + (v || 0), 0); }

function catById(id) { return CATS.find(c => c.id === id) || CATS[CATS.length - 1]; }

function persColors(p) {
  return p === 'C' ? { bg:'#E6F1FB', fg:'#0C447C' }
       : p === 'M' ? { bg:'#EEEDFE', fg:'#3C3489' }
                   : { bg:'#E1F5EE', fg:'#085041' };
}

function allMonthKeys() {
  const ms = new Set();
  data.txs.forEach(t => { if (t.month !== undefined) ms.add(mKey(t.month, t.year)); });
  data.income.forEach(t => { if (t.month !== undefined) ms.add(mKey(t.month, t.year)); });
  Object.keys(data.fixed || {}).forEach(k => {
    if (Object.values(data.fixed[k]).some(v => v > 0)) ms.add(k);
  });
  ms.add(mKey(now.getMonth(), now.getFullYear()));
  return [...ms].sort();
}

function rebuildViewSelect() {
  const sel  = document.getElementById('viewMonthSel');
  const keys = allMonthKeys();
  const cur  = mKey(viewMonth, viewYear);
  if (!keys.includes(cur)) { viewMonth = now.getMonth(); viewYear = now.getFullYear(); }
  sel.innerHTML = keys.map(k => {
    const [y, m] = k.split('-');
    const sel = (+m - 1 === viewMonth && +y === viewYear);
    return `<option value="${k}"${sel ? ' selected' : ''}>${MONTHS[+m - 1]} ${y}</option>`;
  }).join('');
}

/* ── Month change ── */
function onViewMonthChange() {
  const v = document.getElementById('viewMonthSel').value;
  const [y, m] = v.split('-');
  viewMonth   = +m - 1;
  viewYear    = +y;
  editingId   = null;
  editingType = null;
  renderDashboard();
}

/* ── Form ── */
function setFormMode(mode) {
  formMode = mode;
  document.getElementById('tabExpense').classList.toggle('active', mode === 'expense');
  document.getElementById('tabIncome').classList.toggle('active',  mode === 'income');
  renderForm();
}

function renderForm() {
  const row = document.getElementById('formRow');
  if (formMode === 'expense') {
    row.innerHTML = `
      <div class="field fdate"><label>Date</label><input type="date" id="fDate" value="${todayStr()}"></div>
      <div class="field fdesc">
        <label>Description</label>
        <input id="fDesc" placeholder="e.g. Hiperdino" autocomplete="off">
        <div class="suggestion" id="sugBox" style="display:none"></div>
      </div>
      <div class="field famt"><label>Amount (€)</label><input id="fAmt" placeholder="0,00"></div>
      <div class="field fcat"><label>Category</label>
        <select id="fCat">${CATS.map(c => `<option value="${c.id}">${c.label}</option>`).join('')}</select>
      </div>
      <div class="field fpers"><label>Person</label>
        <select id="fPers">
          <option value="B">B — Joint</option>
          <option value="C">C — Colin</option>
          <option value="M">M — Monica</option>
        </select>
      </div>
      <button class="add-btn" onclick="addTx()">Add</button>`;
    const desc = document.getElementById('fDesc');
    desc.addEventListener('input', onDescInput);
    desc.addEventListener('keydown', e => { if (e.key === 'Enter') addTx(); });
    document.getElementById('fAmt').addEventListener('keydown', e => { if (e.key === 'Enter') addTx(); });
  } else {
    row.innerHTML = `
      <div class="field fdate"><label>Date</label><input type="date" id="iDate" value="${todayStr()}"></div>
      <div class="field fdesc"><label>Source / Description</label><input id="iDesc" placeholder="e.g. Colin salary" autocomplete="off"></div>
      <div class="field famt"><label>Amount (€)</label><input id="iAmt" placeholder="0,00"></div>
      <div class="field fpers"><label>Person</label>
        <select id="iPers">
          <option value="C">C — Colin</option>
          <option value="M">M — Monica</option>
          <option value="B">B — Joint</option>
        </select>
      </div>
      <button class="add-btn income-btn" onclick="addIncome()">Add income</button>`;
    document.getElementById('iDesc').addEventListener('keydown', e => { if (e.key === 'Enter') addIncome(); });
    document.getElementById('iAmt').addEventListener('keydown',  e => { if (e.key === 'Enter') addIncome(); });
  }
}

function onDescInput() {
  const q   = document.getElementById('fDesc').value.trim();
  const box = document.getElementById('sugBox');
  if (!box) return;
  if (!q || q.length < 2) { box.style.display = 'none'; return; }
  const match = data.txs
    .filter(t => t.desc.toLowerCase().includes(q.toLowerCase()))
    .sort((a, b) => (b.year * 12 + b.month) - (a.year * 12 + a.month))[0];
  if (match) {
    const c = catById(match.cat);
    const { bg, fg } = persColors(match.pers);
    box.innerHTML = `<div class="sug-item" onmousedown="applySug('${match.id}')">
      <div>
        <div class="sug-name">${match.desc}</div>
        <div class="sug-meta">Last: ${fmtDate(match.date) || MONTHS[match.month] + ' ' + match.year}</div>
      </div>
      <div style="display:flex;gap:5px;align-items:center;flex-shrink:0">
        <span class="sug-badge" style="background:${c.colors[0]};color:${c.colors[2]}">${c.label}</span>
        <span class="sug-badge" style="background:${bg};color:${fg}">${match.pers}</span>
      </div>
    </div>`;
    box.style.display = 'block';
  } else {
    box.style.display = 'none';
  }
}

function applySug(matchId) {
  const match = data.txs.find(t => t.id === matchId);
  if (!match) return;
  document.getElementById('fDesc').value = match.desc;
  document.getElementById('fCat').value  = match.cat;
  document.getElementById('fPers').value = match.pers;
  const box = document.getElementById('sugBox');
  if (box) box.style.display = 'none';
  document.getElementById('fAmt').focus();
}

/* ── Add ── */
function addTx() {
  const desc   = document.getElementById('fDesc').value.trim();
  const amtStr = document.getElementById('fAmt').value;
  const cat    = document.getElementById('fCat').value;
  const pers   = document.getElementById('fPers').value;
  const date   = document.getElementById('fDate').value;
  if (!desc || !amtStr) return;
  const amt = parseAmt(amtStr);
  if (!amt) return;
  const { month, year } = dateToMY(date);
  data.txs.push({ id: Date.now() + '_' + Math.random().toString(36).slice(2), desc, amt, cat, pers, date, month, year });
  saveData();
  document.getElementById('fDesc').value = '';
  document.getElementById('fAmt').value  = '';
  rebuildViewSelect();
  renderDashboard();
}

function addIncome() {
  const desc   = document.getElementById('iDesc').value.trim();
  const amtStr = document.getElementById('iAmt').value;
  const pers   = document.getElementById('iPers').value;
  const date   = document.getElementById('iDate').value;
  if (!desc || !amtStr) return;
  const amt = parseAmt(amtStr);
  if (!amt) return;
  const { month, year } = dateToMY(date);
  data.income.push({ id: Date.now() + '_' + Math.random().toString(36).slice(2), desc, amt, pers, date, month, year });
  saveData();
  document.getElementById('iDesc').value = '';
  document.getElementById('iAmt').value  = '';
  rebuildViewSelect();
  renderDashboard();
}

/* ── Fixed costs ── */
function saveFixed(fieldId) {
  const key = mKey(viewMonth, viewYear);
  if (!data.fixed[key]) data.fixed[key] = {};
  const amt = parseAmt(document.getElementById('fixed_' + fieldId).value);
  if (!amt) delete data.fixed[key][fieldId];
  else data.fixed[key][fieldId] = amt;
  if (!Object.keys(data.fixed[key]).length) delete data.fixed[key];
  saveData();
  rebuildViewSelect();
  renderFixed();
  renderDelta();
  renderStats();
  renderCatList();
  renderPies();
}

/* ── Edit ── */
function startEdit(id, type) { editingId = id; editingType = type; renderLists(); }
function cancelEdit()        { editingId = null; editingType = null; renderLists(); }

function saveEdit(id, type) {
  const arr = type === 'income' ? data.income : data.txs;
  const tx  = arr.find(t => t.id === id);
  if (!tx) { cancelEdit(); return; }
  const newDesc = document.getElementById('ed_desc').value.trim();
  const newAmt  = parseAmt(document.getElementById('ed_amt').value);
  const newDate = document.getElementById('ed_date').value;
  const newPers = document.getElementById('ed_pers').value;
  if (!newDesc || !newAmt) { cancelEdit(); return; }
  tx.desc = newDesc; tx.amt = newAmt; tx.date = newDate; tx.pers = newPers;
  const { month, year } = dateToMY(newDate);
  tx.month = month; tx.year = year;
  if (type === 'expense') tx.cat = document.getElementById('ed_cat').value;
  editingId = null; editingType = null;
  saveData(); rebuildViewSelect(); renderDashboard();
}

function deleteTx(id)     { data.txs    = data.txs.filter(t => t.id !== id);    saveData(); rebuildViewSelect(); renderDashboard(); }
function deleteIncome(id) { data.income = data.income.filter(t => t.id !== id); saveData(); rebuildViewSelect(); renderDashboard(); }

function editRowExpense(tx) {
  const catOpts  = CATS.map(c => `<option value="${c.id}"${tx.cat === c.id ? ' selected' : ''}>${c.label}</option>`).join('');
  const persOpts = ['B','C','M'].map(p => `<option value="${p}"${tx.pers === p ? ' selected' : ''}>${p}</option>`).join('');
  return `<div class="tx-row editing col-expense">
    <div><input class="edit-input" id="ed_date" type="date" value="${tx.date || ''}"></div>
    <div><input class="edit-input" id="ed_desc" value="${tx.desc.replace(/"/g,'&quot;')}" style="width:100%"></div>
    <div><input class="edit-input" id="ed_amt" value="${fmt(tx.amt)}" style="text-align:right"></div>
    <div><select class="edit-select" id="ed_cat">${catOpts}</select></div>
    <div><select class="edit-select" id="ed_pers">${persOpts}</select></div>
    <div class="action-btns">
      <button class="btn-sm btn-save" onclick="saveEdit('${tx.id}','expense')">Save</button>
      <button class="btn-sm" onclick="cancelEdit()">✕</button>
    </div>
  </div>`;
}

function editRowIncome(tx) {
  const persOpts = ['C','M','B'].map(p => `<option value="${p}"${tx.pers === p ? ' selected' : ''}>${p}</option>`).join('');
  return `<div class="tx-row editing col-income">
    <div><input class="edit-input" id="ed_date" type="date" value="${tx.date || ''}"></div>
    <div><input class="edit-input" id="ed_desc" value="${tx.desc.replace(/"/g,'&quot;')}" style="width:100%"></div>
    <div><input class="edit-input" id="ed_amt" value="${fmt(tx.amt)}" style="text-align:right"></div>
    <div><select class="edit-select" id="ed_pers">${persOpts}</select></div>
    <div class="action-btns">
      <button class="btn-sm btn-save" onclick="saveEdit('${tx.id}','income')">Save</button>
      <button class="btn-sm" onclick="cancelEdit()">✕</button>
    </div>
  </div>`;
}

/* ── Render pieces ── */
function renderDelta() {
  const txs        = getViewTxs();
  const inc        = getViewIncome();
  const totalInc   = inc.reduce((s, t) => s + t.amt, 0);
  const totalExp   = txs.reduce((s, t) => s + t.amt, 0) + fixedTotal();
  const delta      = totalInc - totalExp;
  const dClass     = delta > 0 ? 'dc-positive' : delta < 0 ? 'dc-negative' : 'dc-zero';
  const sign       = delta > 0 ? '+' : delta < 0 ? '−' : '';
  document.getElementById('deltaBar').innerHTML = `
    <div class="delta-card dc-income">
      <div class="lbl">Total income</div>
      <div class="val">${fmt(totalInc)} €</div>
      <div class="sub">${inc.length} entr${inc.length === 1 ? 'y' : 'ies'}</div>
    </div>
    <div class="delta-card dc-expense">
      <div class="lbl">Total expenses</div>
      <div class="val">${fmt(totalExp)} €</div>
      <div class="sub">incl. fixed costs</div>
    </div>
    <div class="delta-card ${dClass}">
      <div class="lbl">Monthly delta</div>
      <div class="val">${sign}${fmt(Math.abs(delta))} €</div>
      <div class="sub">${delta >= 0 ? 'Surplus this month' : 'Deficit this month'}</div>
    </div>`;
}

function renderStats() {
  const txs    = getViewTxs();
  const ft     = fixedTotal();
  const total  = txs.reduce((s, t) => s + t.amt, 0) + ft;
  const colin  = txs.filter(t => t.pers === 'C').reduce((s,t) => s + t.amt, 0)
               + txs.filter(t => t.pers === 'B').reduce((s,t) => s + t.amt / 2, 0)
               + ft / 2;
  const monica = txs.filter(t => t.pers === 'M').reduce((s,t) => s + t.amt, 0)
               + txs.filter(t => t.pers === 'B').reduce((s,t) => s + t.amt / 2, 0)
               + ft / 2;
  document.getElementById('statsRow').innerHTML = `
    <div class="stat"><div class="lbl">Total spent</div><div class="val red">${fmt(total)} €</div></div>
    <div class="stat"><div class="lbl">Colin's share</div><div class="val blue">${fmt(colin)} €</div></div>
    <div class="stat"><div class="lbl">Monica's share</div><div class="val purple">${fmt(monica)} €</div></div>
    <div class="stat"><div class="lbl">Transactions</div><div class="val">${txs.length}</div></div>`;
}

function renderFixed() {
  const fixed = getFixed();
  const total = fixedTotal();
  let html = `<div class="fixed-header">
    <div class="card-title">Rent &amp; fixed costs</div>
    ${total > 0 ? `<div class="fixed-total-badge">${fmt(total)} €</div>` : ''}
  </div>
  <div class="fixed-grid">`;
  FIXED_FIELDS.forEach(f => {
    const val = fixed[f.id] ? fmt(fixed[f.id]) : '';
    html += `<div class="fixed-item">
      <div class="fixed-label"><span style="font-size:13px">${f.icon}</span>${f.label}</div>
      <input class="fixed-input${fixed[f.id] ? ' filled' : ''}" id="fixed_${f.id}"
        placeholder="${f.placeholder}" value="${val}"
        onkeydown="if(event.key==='Enter')this.blur()"
        onblur="saveFixed('${f.id}')">
    </div>`;
  });
  html += `</div>`;
  document.getElementById('fixedCard').innerHTML = html;
}

function renderCatList() {
  const txs   = getViewTxs();
  const total = txs.reduce((s, t) => s + t.amt, 0) + fixedTotal();
  document.getElementById('catList').innerHTML = CATS.map(c => {
    let sum = txs.filter(t => t.cat === c.id).reduce((s, t) => s + t.amt, 0);
    if (c.id === 'rent') sum += fixedTotal();
    const pct = total > 0 ? Math.round(sum / total * 100) : 0;
    return `<div class="cat-row${activeCat === c.id ? ' active' : ''}" onclick="filterCat('${c.id}')">
      <div class="cat-dot" style="background:${c.colors[1]}"></div>
      <div class="cat-name">${c.label}</div>
      <div class="cat-amt" style="color:${c.colors[2]}">${fmt(sum)} €</div>
      <div class="cat-pct">${pct}%</div>
    </div>`;
  }).join('');
}

function filterCat(id) { activeCat = activeCat === id ? null : id; renderLists(); }

function renderPies() {
  const txs   = getViewTxs();
  const ft    = fixedTotal();
  const total = txs.reduce((s, t) => s + t.amt, 0) + ft;
  const sums  = CATS.map(c => {
    let s = txs.filter(t => t.cat === c.id).reduce((a, t) => a + t.amt, 0);
    if (c.id === 'rent') s += ft;
    return parseFloat(s.toFixed(2));
  });

  document.getElementById('pieLeg').innerHTML = CATS.map((c, i) => {
    const pct = total > 0 ? Math.round(sums[i] / total * 100) : 0;
    if (!sums[i]) return '';
    return `<span><span class="sq" style="background:${c.colors[1]}"></span>${c.label} ${pct}%</span>`;
  }).join('');

  if (pieChart) pieChart.destroy();
  pieChart = new Chart(document.getElementById('pieChart'), {
    type: 'doughnut',
    data: {
      labels: CATS.map(c => c.label),
      datasets: [{ data: sums, backgroundColor: CATS.map(c => c.colors[1]), borderColor: CATS.map(c => c.colors[0]), borderWidth: 2, hoverOffset: 6 }]
    },
    options: {
      responsive: true, maintainAspectRatio: false, cutout: '58%',
      plugins: {
        legend: { display: false },
        tooltip: { callbacks: { label: ctx => {
          const pct = total > 0 ? Math.round(ctx.parsed / total * 100) : 0;
          return `${ctx.label}: ${fmt(ctx.parsed)} € (${pct}%)`;
        }}}
      }
    }
  });

  const colin  = txs.filter(t => t.pers === 'C').reduce((s,t) => s + t.amt, 0)
               + txs.filter(t => t.pers === 'B').reduce((s,t) => s + t.amt / 2, 0) + ft / 2;
  const monica = txs.filter(t => t.pers === 'M').reduce((s,t) => s + t.amt, 0)
               + txs.filter(t => t.pers === 'B').reduce((s,t) => s + t.amt / 2, 0) + ft / 2;
  const pctC   = total > 0 ? Math.round(colin  / total * 100) : 0;
  const pctM   = total > 0 ? Math.round(monica / total * 100) : 0;

  document.getElementById('pie2Leg').innerHTML = `
    <span><span class="sq" style="background:#378ADD"></span>Colin ${pctC}% (${fmt(colin)} €)</span>
    <span><span class="sq" style="background:#7F77DD"></span>Monica ${pctM}% (${fmt(monica)} €)</span>`;

  if (pie2Chart) pie2Chart.destroy();
  pie2Chart = new Chart(document.getElementById('pie2Chart'), {
    type: 'pie',
    data: {
      labels: ['Colin', 'Monica'],
      datasets: [{ data: [parseFloat(colin.toFixed(2)), parseFloat(monica.toFixed(2))], backgroundColor: ['#378ADD','#7F77DD'], borderColor: ['#E6F1FB','#EEEDFE'], borderWidth: 2, hoverOffset: 6 }]
    },
    options: {
      responsive: true, maintainAspectRatio: false,
      plugins: {
        legend: { display: false },
        tooltip: { callbacks: { label: ctx => {
          const pct = total > 0 ? Math.round(ctx.parsed / total * 100) : 0;
          return `${ctx.label}: ${fmt(ctx.parsed)} € (${pct}%)`;
        }}}
      }
    }
  });
}

function renderLists() {
  /* Income */
  const inc = getViewIncome().slice().sort((a, b) => (b.date || '').localeCompare(a.date || ''));
  const sec = document.getElementById('incomeSection');
  if (!inc.length) {
    sec.innerHTML = `<div class="empty">No income recorded for this month</div>`;
  } else {
    let html = `<div class="tx-head col-income" style="background:var(--green-light)">
      <div>Date</div><div>Source</div><div>Amount</div><div>Person</div><div></div>
    </div>`;
    inc.forEach(t => {
      if (editingId === t.id && editingType === 'income') { html += editRowIncome(t); return; }
      const { bg, fg } = persColors(t.pers);
      html += `<div class="tx-row col-income">
        <div class="date-cell">${fmtDate(t.date)}</div>
        <div class="desc-cell">${t.desc}</div>
        <div class="amt-cell" style="color:var(--green)">${fmt(t.amt)} €</div>
        <div><span class="badge" style="background:${bg};color:${fg}">${t.pers}</span></div>
        <div class="action-btns">
          <button class="btn-sm" onclick="startEdit('${t.id}','income')">Edit</button>
          <button class="btn-sm btn-del" onclick="deleteIncome('${t.id}')">Del</button>
        </div>
      </div>`;
    });
    sec.innerHTML = html;
  }

  /* Expenses */
  const txs = getViewTxs()
    .filter(t => !activeCat || t.cat === activeCat)
    .slice()
    .sort((a, b) => (b.date || '').localeCompare(a.date || ''));
  const el = document.getElementById('txList');
  if (!txs.length) { el.innerHTML = `<div class="empty">No expenses for this month</div>`; return; }

  const byCat = {};
  CATS.forEach(c => { byCat[c.id] = []; });
  txs.forEach(t => { if (byCat[t.cat]) byCat[t.cat].push(t); });

  let html = `<div class="tx-head col-expense">
    <div>Date</div><div>Description</div><div>Amount</div><div>Category</div><div>Person</div><div></div>
  </div>`;
  CATS.forEach(c => {
    const ctxs = byCat[c.id];
    if (!ctxs.length) return;
    html += `<div class="section-sep" style="color:${c.colors[2]};background:${c.colors[0]}">${c.label}</div>`;
    ctxs.forEach(t => {
      if (editingId === t.id && editingType === 'expense') { html += editRowExpense(t); return; }
      const { bg, fg } = persColors(t.pers);
      html += `<div class="tx-row col-expense">
        <div class="date-cell">${fmtDate(t.date)}</div>
        <div class="desc-cell" title="${t.desc}">${t.desc}</div>
        <div class="amt-cell" style="color:var(--red)">${fmt(t.amt)} €</div>
        <div><span class="badge" style="background:${c.colors[0]};color:${c.colors[2]}">${c.label}</span></div>
        <div><span class="badge" style="background:${bg};color:${fg}">${t.pers}</span></div>
        <div class="action-btns">
          <button class="btn-sm" onclick="startEdit('${t.id}','expense')">Edit</button>
          <button class="btn-sm btn-del" onclick="deleteTx('${t.id}')">Del</button>
        </div>
      </div>`;
    });
  });
  el.innerHTML = html;
}

/* ── Full render ── */
function renderDashboard() {
  rebuildViewSelect();
  renderDelta();
  renderStats();
  renderFixed();
  renderCatList();
  renderPies();
  renderLists();
}

/* ── Init ── */
renderForm();
renderDashboard();
</script>
</body>
</html>
