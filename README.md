<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
<meta name="theme-color" content="#0f172a">
<title>TableFlow POS — Table Billing</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;500;600;700;800&display=swap" rel="stylesheet">
<script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
<style>
  :root {
    --bg: #f1f5f9;
    --surface: #ffffff;
    --surface-subtle: #f8fafc;
    --ink: #0f172a;
    --ink-muted: #64748b;
    --ink-light: #94a3b8;
    --border: #e2e8f0;
    --primary: #ea580c;
    --primary-hover: #c2410c;
    --primary-soft: #fff7ed;
    --primary-border: #fed7aa;
    --green: #16a34a;
    --green-soft: #f0fdf4;
    --red: #dc2626;
    --red-soft: #fef2f2;
    --amber: #d97706;
    --amber-soft: #fffbeb;
    --shadow-sm: 0 1px 2px rgba(15, 23, 42, 0.05);
    --shadow-md: 0 4px 12px -2px rgba(15, 23, 42, 0.08), 0 2px 4px -2px rgba(15, 23, 42, 0.04);
    --shadow-lg: 0 12px 24px -4px rgba(15, 23, 42, 0.14);
    --radius: 14px;
    --radius-sm: 10px;
  }

  * { margin: 0; padding: 0; box-sizing: border-box; -webkit-tap-highlight-color: transparent; }

  body {
    font-family: 'Plus Jakarta Sans', -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
    background: var(--bg);
    color: var(--ink);
    padding-bottom: 84px;
    line-height: 1.45;
    -webkit-font-smoothing: antialiased;
  }

  /* Header */
  header {
    background: #0f172a;
    color: #fff;
    padding: 14px 18px;
    display: flex;
    justify-content: space-between;
    align-items: center;
    position: sticky;
    top: 0;
    z-index: 30;
    box-shadow: 0 2px 10px rgba(15, 23, 42, 0.15);
  }
  .header-left { display: flex; align-items: center; gap: 10px; }
  .back-btn {
    background: rgba(255,255,255,0.12);
    border: none;
    color: #fff;
    width: 34px;
    height: 34px;
    border-radius: 10px;
    font-size: 16px;
    cursor: pointer;
    display: flex;
    align-items: center;
    justify-content: center;
  }
  .back-btn:active { background: rgba(255,255,255,0.22); }
  #h-title { font-size: 17px; font-weight: 700; letter-spacing: -0.01em; }
  header .sub {
    font-size: 12px;
    font-weight: 600;
    color: #cbd5e1;
    background: rgba(255,255,255,0.08);
    padding: 4px 10px;
    border-radius: 20px;
  }

  #app { padding: 16px; max-width: 680px; margin: 0 auto; }

  /* Bottom Navigation */
  nav {
    position: fixed;
    bottom: 0; left: 0; right: 0;
    background: rgba(255, 255, 255, 0.96);
    backdrop-filter: blur(10px);
    display: flex;
    border-top: 1px solid var(--border);
    z-index: 30;
    padding: 4px 6px calc(6px + env(safe-area-inset-bottom));
  }
  nav button {
    flex: 1;
    padding: 6px 2px;
    border: none;
    background: none;
    font-family: inherit;
    font-size: 11px;
    font-weight: 600;
    color: var(--ink-muted);
    cursor: pointer;
    border-radius: 10px;
    transition: all 0.15s ease;
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 2px;
  }
  nav button .nav-ico {
    font-size: 19px;
    line-height: 1.2;
    padding: 2px 12px;
    border-radius: 14px;
    transition: background 0.15s ease;
  }
  nav button.active { color: var(--primary); }
  nav button.active .nav-ico { background: var(--primary-soft); }

  /* Cards & Layout */
  .card {
    background: var(--surface);
    border-radius: var(--radius);
    padding: 16px;
    margin-bottom: 14px;
    border: 1px solid var(--border);
    box-shadow: var(--shadow-sm);
  }
  .section-title {
    font-size: 12px;
    font-weight: 700;
    color: var(--ink-muted);
    text-transform: uppercase;
    letter-spacing: 0.06em;
    margin: 18px 0 8px 2px;
    display: flex;
    justify-content: space-between;
    align-items: center;
  }

  /* KPI Strip */
  .kpi-row {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 10px;
    margin-bottom: 16px;
  }
  .kpi-card {
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: var(--radius);
    padding: 12px 14px;
    box-shadow: var(--shadow-sm);
  }
  .kpi-label { font-size: 11px; font-weight: 700; color: var(--ink-muted); text-transform: uppercase; letter-spacing: 0.04em; }
  .kpi-val { font-size: 20px; font-weight: 800; color: var(--ink); margin-top: 2px; letter-spacing: -0.02em; }
  .kpi-val.accent { color: var(--primary); }

  /* Table Grid */
  .grid {
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(152px, 1fr));
    gap: 12px;
  }
  .table-box {
    background: var(--surface);
    border-radius: var(--radius);
    padding: 14px;
    box-shadow: var(--shadow-sm);
    cursor: pointer;
    border: 1.5px solid var(--border);
    transition: transform 0.12s ease, box-shadow 0.12s ease;
    position: relative;
    display: flex;
    flex-direction: column;
    justify-content: space-between;
    min-height: 112px;
  }
  .table-box:active { transform: scale(0.97); }
  .table-box.active {
    border-color: var(--primary);
    background: linear-gradient(180deg, #fff7ed 0%, #ffffff 48%);
    box-shadow: var(--shadow-md);
  }
  .table-top { display: flex; justify-content: space-between; align-items: flex-start; gap: 6px; }
  .tname { font-weight: 800; font-size: 16px; color: var(--ink); }
  .badge {
    font-size: 10px;
    font-weight: 700;
    padding: 3px 7px;
    border-radius: 6px;
    text-transform: uppercase;
    letter-spacing: 0.03em;
    white-space: nowrap;
  }
  .badge-live { background: var(--primary); color: #fff; }
  .badge-free { background: var(--green-soft); color: var(--green); }
  .tinfo { font-size: 12px; color: var(--ink-muted); margin-top: 6px; font-weight: 500; }
  .tcust { font-size: 12px; color: var(--ink); font-weight: 600; margin-top: 2px; white-space: nowrap; overflow: hidden; text-overflow: ellipsis; }
  .ttotal { font-weight: 800; color: var(--primary); margin-top: 10px; font-size: 17px; letter-spacing: -0.01em; }
  .table-box.free-t {
    background: var(--surface-subtle);
    border: 1.5px dashed #cbd5e1;
  }
  .table-box.free-t .tname { color: #334155; }
  .free-cta { font-size: 12px; font-weight: 700; color: var(--primary); margin-top: 12px; }

  /* Inputs & Buttons */
  label { font-size: 12px; font-weight: 700; color: #334155; display: block; margin-top: 12px; }
  label:first-child { margin-top: 0; }
  input, select {
    width: 100%;
    padding: 11px 13px;
    border: 1.5px solid var(--border);
    border-radius: var(--radius-sm);
    font-family: inherit;
    font-size: 15px;
    font-weight: 500;
    color: var(--ink);
    margin-top: 5px;
    background: var(--surface);
    transition: border-color 0.15s ease, box-shadow 0.15s ease;
  }
  input:focus, select:focus {
    outline: none;
    border-color: var(--primary);
    box-shadow: 0 0 0 3px rgba(234, 88, 12, 0.14);
  }

  .btn {
    display: flex;
    align-items: center;
    justify-content: center;
    gap: 8px;
    width: 100%;
    padding: 13px 16px;
    border: none;
    border-radius: var(--radius-sm);
    font-family: inherit;
    font-size: 15px;
    font-weight: 700;
    cursor: pointer;
    margin-top: 10px;
    transition: transform 0.1s ease, opacity 0.15s ease;
  }
  .btn:active { transform: scale(0.985); }
  .btn-primary { background: var(--primary); color: #fff; box-shadow: 0 2px 6px rgba(234, 88, 12, 0.25); }
  .btn-green { background: var(--green); color: #fff; box-shadow: 0 2px 6px rgba(22, 163, 74, 0.22); }
  .btn-red { background: var(--red-soft); color: var(--red); border: 1px solid #fecaca; }
  .btn-gray { background: #e2e8f0; color: #334155; }
  .btn-sm { width: auto; display: inline-flex; padding: 8px 13px; font-size: 13px; margin-top: 0; border-radius: 8px; }

  .row-flex { display: flex; gap: 8px; align-items: center; }
  .grid-2 { display: grid; grid-template-columns: 1fr 1fr; gap: 10px; }

  /* Chips */
  .chip {
    display: inline-flex;
    align-items: center;
    background: var(--surface-subtle);
    border: 1px solid var(--border);
    border-radius: 20px;
    padding: 6px 12px;
    margin: 6px 6px 0 0;
    font-size: 13px;
    font-weight: 600;
  }
  .chip b { cursor: pointer; margin-left: 8px; color: var(--red); font-size: 14px; }

  /* Order Lines & Menu Picker */
  .item-row {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 11px 2px;
    border-bottom: 1px solid #f1f5f9;
    gap: 10px;
  }
  .item-row:last-child { border-bottom: none; padding-bottom: 2px; }
  .item-row:first-child { padding-top: 2px; }
  .item-row.clickable { cursor: pointer; border-radius: 8px; padding: 11px 6px; }
  .item-row.clickable:active { background: var(--primary-soft); }
  .iname { font-size: 15px; font-weight: 600; color: var(--ink); }
  .iprice { font-size: 13px; color: var(--ink-muted); font-weight: 500; margin-top: 1px; }

  .qty-ctrl { display: flex; align-items: center; gap: 8px; }
  .qty-ctrl button {
    width: 32px; height: 32px;
    border: none;
    border-radius: 8px;
    font-size: 18px;
    font-weight: 700;
    cursor: pointer;
    display: flex;
    align-items: center;
    justify-content: center;
  }
  .qminus { background: var(--red-soft); color: var(--red); }
  .qplus { background: var(--primary-soft); color: var(--primary); }
  .qty-num { min-width: 24px; text-align: center; font-weight: 800; font-size: 15px; }

  .add-pill {
    display: inline-flex;
    align-items: center;
    gap: 4px;
    background: var(--primary-soft);
    color: var(--primary);
    border: 1px solid var(--primary-border);
    padding: 6px 12px;
    border-radius: 20px;
    font-size: 13px;
    font-weight: 700;
  }
  .add-pill.in-cart {
    background: var(--primary);
    color: #fff;
    border-color: var(--primary);
  }

  /* Sticky Order Action Dock */
  .sticky-dock {
    position: sticky;
    bottom: 74px;
    background: #0f172a;
    color: #fff;
    border-radius: var(--radius);
    padding: 14px 16px;
    margin-top: 16px;
    box-shadow: var(--shadow-lg);
    z-index: 20;
  }
  .dock-top {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 10px;
  }
  .dock-label { font-size: 12px; color: #94a3b8; font-weight: 600; }
  .dock-total { font-size: 21px; font-weight: 800; color: #fff; letter-spacing: -0.02em; }
  .dock-actions { display: grid; grid-template-columns: 1fr 1fr; gap: 8px; }
  .dock-actions .btn { margin-top: 0; padding: 11px 12px; font-size: 14px; }

  /* Receipt Card */
  .receipt {
    background: #fff;
    border-radius: var(--radius);
    padding: 20px 18px;
    border: 1px solid var(--border);
    box-shadow: var(--shadow-md);
    position: relative;
  }
  .receipt-divider {
    border-top: 2px dashed #cbd5e1;
    margin: 14px 0;
  }
  .list-line {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 7px 0;
    font-size: 14px;
  }

  /* Empty State */
  .empty-state { text-align: center; padding: 54px 20px; color: var(--ink-muted); }
  .empty-state .face { font-size: 48px; margin-bottom: 10px; }
  .empty-state p { font-size: 16px; font-weight: 600; color: #475569; }
  .empty-state span { font-size: 13px; display: block; margin-top: 4px; }

  /* Toast Notification */
  #toast {
    position: fixed;
    top: 68px;
    left: 50%;
    transform: translateX(-50%) translateY(-16px);
    background: #0f172a;
    color: #fff;
    padding: 10px 18px;
    border-radius: 30px;
    font-size: 13px;
    font-weight: 600;
    box-shadow: var(--shadow-lg);
    opacity: 0;
    pointer-events: none;
    transition: all 0.22s ease;
    z-index: 100;
    white-space: nowrap;
  }
  #toast.show { opacity: 1; transform: translateX(-50%) translateY(0); }
</style>
</head>
<body>

<header>
  <div class="header-left">
    <button id="h-back" class="back-btn" style="display:none" onclick="goHome()">←</button>
    <div id="h-title">TableFlow POS</div>
  </div>
  <div class="sub" id="h-sub"></div>
</header>

<div id="toast"></div>
<div id="app"></div>

<nav id="nav" style="display:none">
  <button onclick="goHome()" id="nav-home"><span class="nav-ico">🪑</span>Tables</button>
  <button onclick="goMenu()" id="nav-menu"><span class="nav-ico">🍽️</span>Menu</button>
  <button onclick="goHistory()" id="nav-history"><span class="nav-ico">🧾</span>Bills</button>
  <button onclick="goExport()" id="nav-export"><span class="nav-ico">📊</span>Sales</button>
  <button onclick="goSettings()" id="nav-settings"><span class="nav-ico">⚙️</span>Setup</button>
</nav>

<script>
/* ---------- IndexedDB & Helpers ---------- */
let db, shop = null, draft = null, cachedMenu = [], menuFilter = '';

function openDB() {
  return new Promise((res, rej) => {
    const r = indexedDB.open('restoBillDB', 1);
    r.onupgradeneeded = e => {
      const d = e.target.result;
      d.createObjectStore('settings');
      d.createObjectStore('menu', { keyPath: 'id', autoIncrement: true });
      d.createObjectStore('tables', { keyPath: 'id', autoIncrement: true });
      d.createObjectStore('active', { keyPath: 'id' });
      d.createObjectStore('history', { keyPath: 'id' });
    };
    r.onsuccess = e => { db = e.target.result; res(); };
    r.onerror = () => rej(r.error);
  });
}

const tx = (s, m) => db.transaction(s, m || 'readonly').objectStore(s);
const rp = r => new Promise((res, rej) => { r.onsuccess = () => res(r.result); r.onerror = () => rej(r.error); });
const getAll = s => rp(tx(s).getAll());
const getOne = (s, k) => rp(tx(s).get(k));
const putV = (s, v) => rp(tx(s, 'readwrite').put(v));
const delV = (s, k) => rp(tx(s, 'readwrite').delete(k));
const uid = () => Date.now().toString(36) + Math.random().toString(36).slice(2, 7);
const esc = s => String(s == null ? '' : s).replace(/[&<>"']/g, c => ({ '&': '&amp;', '<': '&lt;', '>': '&gt;', '"': '&quot;', "'": '&#39;' }[c]));
const money = n => (shop && shop.currency ? shop.currency : '₹') + Number(n || 0).toFixed(2);
const fmtT = ts => { const d = new Date(ts); return d.getHours().toString().padStart(2, '0') + ':' + d.getMinutes().toString().padStart(2, '0'); };
const fmtD = ts => { const d = new Date(ts); return d.getDate() + '/' + (d.getMonth() + 1) + '/' + d.getFullYear(); };
function minsAgo(ts) { return Math.max(0, Math.round((Date.now() - ts) / 60000)); }

let toastTimer;
function showToast(msg) {
  const el = document.getElementById('toast');
  el.textContent = msg;
  el.classList.add('show');
  clearTimeout(toastTimer);
  toastTimer = setTimeout(() => el.classList.remove('show'), 2200);
}

function setNav(id) {
  document.querySelectorAll('nav button').forEach(b => b.classList.remove('active'));
  if (id) {
    document.getElementById(id).classList.add('active');
    document.getElementById('nav').style.display = 'flex';
    document.body.style.paddingBottom = '84px';
  } else {
    document.getElementById('nav').style.display = 'none';
    document.body.style.paddingBottom = '20px';
  }
}

function setHeader(t, s, showBack = false) {
  document.getElementById('h-title').textContent = t;
  const subEl = document.getElementById('h-sub');
  subEl.textContent = s || '';
  subEl.style.display = s ? 'block' : 'none';
  document.getElementById('h-back').style.display = showBack ? 'flex' : 'none';
}

/* ---------- SETUP ---------- */
let setupTables = [];
async function renderSetup() {
  setNav(null);
  setHeader('Shop Setup', 'Configuration', Boolean(shop && shop.done));
  setupTables = (await getAll('tables')).map(t => t.name);
  document.getElementById('app').innerHTML = `
    <div class="card">
      <label>Restaurant / Shop Name</label>
      <input id="s-name" placeholder="e.g. Athang South Indian" value="${shop ? esc(shop.name) : ''}">
      <div class="grid-2">
        <div>
          <label>Phone (for Bills)</label>
          <input id="s-phone" type="tel" placeholder="9876543210" value="${shop ? esc(shop.phone) : ''}">
        </div>
        <div>
          <label>Currency</label>
          <input id="s-cur" placeholder="₹" value="${shop ? esc(shop.currency || '₹') : '₹'}" maxlength="3">
        </div>
      </div>
      <label>Tables / Counters (Type name & tap Add)</label>
      <div class="row-flex" style="margin-top:5px">
        <input id="s-table" placeholder="e.g. T1, T2, Parcel-1" style="margin-top:0" onkeydown="if(event.key==='Enter')addSetupTable()">
        <button class="btn btn-gray btn-sm" style="padding:11px 16px" onclick="addSetupTable()">+ Add</button>
      </div>
      <div id="setup-chips" style="margin-top:6px">${renderSetupChips()}</div>
      <button class="btn btn-primary" style="margin-top:18px" onclick="saveSetup()">Save & Open POS</button>
    </div>`;
}

function renderSetupChips() {
  if (!setupTables.length) return '<div style="font-size:12px;color:var(--ink-light);margin-top:6px">No tables added yet.</div>';
  return setupTables.map((t, i) => `<span class="chip">${esc(t)}<b onclick="rmSetupTable(${i})">✕</b></span>`).join('');
}

function addSetupTable() {
  const inp = document.getElementById('s-table');
  const v = inp.value.trim();
  if (!v) return;
  if (setupTables.some(t => t.toLowerCase() === v.toLowerCase())) return showToast('Table already exists');
  setupTables.push(v);
  inp.value = '';
  document.getElementById('setup-chips').innerHTML = renderSetupChips();
}

function rmSetupTable(i) {
  setupTables.splice(i, 1);
  document.getElementById('setup-chips').innerHTML = renderSetupChips();
}

async function saveSetup() {
  const name = document.getElementById('s-name').value.trim();
  if (!name) return showToast('Please enter shop name');
  if (setupTables.length === 0) return showToast('Add at least one table');
  shop = {
    name,
    phone: document.getElementById('s-phone').value.trim(),
    currency: document.getElementById('s-cur').value.trim() || '₹',
    billNo: shop ? shop.billNo || 1 : 1,
    done: true
  };
  await rp(tx('settings', 'readwrite').put(shop, 'shop'));
  const old = await getAll('tables');
  for (const t of old) await delV('tables', t.id);
  for (const n of setupTables) await putV('tables', { name: n });
  showToast('Shop settings saved');
  goHome();
}

/* ---------- HOME : Floor Overview ---------- */
async function goHome() {
  setNav('nav-home');
  setHeader(shop ? shop.name : 'TableFlow POS', 'Floor View', false);

  const [act, tables] = [await getAll('active'), await getAll('tables')];
  act.sort((a, b) => a.createdAt - b.createdAt);

  const liveRevenue = act.reduce((sum, o) => sum + o.items.reduce((s, i) => s + i.qty * i.price, 0), 0);
  const freeTables = tables.filter(t => !act.some(o => o.tableName === t.name));

  let html = `
    <div class="kpi-row">
      <div class="kpi-card">
        <div class="kpi-label">Active Tables</div>
        <div class="kpi-val">${act.length} <span style="font-size:13px;font-weight:600;color:var(--ink-muted)">/ ${tables.length}</span></div>
      </div>
      <div class="kpi-card">
        <div class="kpi-label">Running Unbilled</div>
        <div class="kpi-val accent">${money(liveRevenue)}</div>
      </div>
    </div>`;

  if (act.length > 0) {
    html += `<div class="section-title"><span>Occupied Tables (${act.length})</span><span>Tap to edit / settle</span></div>`;
    html += `<div class="grid">` + act.map(o => {
      const count = o.items.reduce((s, i) => s + i.qty, 0);
      const total = o.items.reduce((s, i) => s + i.qty * i.price, 0);
      return `
        <div class="table-box active" onclick="openOrder('${o.id}')">
          <div>
            <div class="table-top">
              <div class="tname">${esc(o.tableName)}</div>
              <span class="badge badge-live">${minsAgo(o.createdAt)}m</span>
            </div>
            <div class="tinfo">${count} ${count === 1 ? 'item' : 'items'} · ${fmtT(o.createdAt)}</div>
            ${o.customerName ? `<div class="tcust">👤 ${esc(o.customerName)}</div>` : ''}
          </div>
          <div class="ttotal">${money(total)}</div>
        </div>`;
    }).join('') + `</div>`;
  } else {
    html += `
      <div class="card" style="text-align:center;padding:24px 16px;background:var(--surface-subtle)">
        <div style="font-size:32px;margin-bottom:6px">✨</div>
        <div style="font-weight:700;font-size:15px">All tables are currently free</div>
        <div style="font-size:13px;color:var(--ink-muted);margin-top:2px">Tap any table below to start a new order</div>
      </div>`;
  }

  if (freeTables.length > 0) {
    html += `<div class="section-title"><span>Available Tables (${freeTables.length})</span><span>Tap to seat</span></div>`;
    html += `<div class="grid">` + freeTables.map(t => `
      <div class="table-box free-t" onclick="goNewOrder('${esc(t.name)}')">
        <div class="table-top">
          <div class="tname">${esc(t.name)}</div>
          <span class="badge badge-free">Free</span>
        </div>
        <div class="free-cta">+ Start Order</div>
      </div>`).join('') + `</div>`;
  }

  document.getElementById('app').innerHTML = html;
}

/* ---------- NEW & EDIT ORDER ---------- */
async function goNewOrder(preselectedTable = null) {
  const [tables, act, menu] = [await getAll('tables'), await getAll('active'), await getAll('menu')];
  cachedMenu = menu;
  const free = tables.filter(t => !act.some(o => o.tableName === t.name));
  if (free.length === 0) return showToast('All tables occupied! Settle a bill first.');
  if (menu.length === 0) {
    showToast('Please add menu items first');
    return goMenu();
  }
  const chosen = preselectedTable && free.some(t => t.name === preselectedTable) ? preselectedTable : free[0].name;
  draft = { id: null, tableName: chosen, customerName: '', customerPhone: '', items: [] };
  menuFilter = '';
  renderOrderForm(free, true);
}

/* BUG FIX: Properly compute selectable tables (current table + free tables) when opening an existing order */
async function openOrder(id) {
  const o = await getOne('active', id);
  if (!o) {
    showToast('Order no longer active');
    return goHome();
  }
  const [tables, act] = [await getAll('tables'), await getAll('active')];
  await loadMenuCache();

  const availableTables = tables.filter(t => t.name === o.tableName || !act.some(a => a.tableName === t.name));
  if (!availableTables.some(t => t.name === o.tableName)) {
    availableTables.unshift({ name: o.tableName });
  }

  draft = {
    id: o.id,
    tableName: o.tableName,
    customerName: o.customerName || '',
    customerPhone: o.customerPhone || '',
    items: JSON.parse(JSON.stringify(o.items))
  };
  menuFilter = '';
  renderOrderForm(availableTables, false);
}

function renderOrderForm(selectableTables, isNew) {
  setNav('nav-home');
  setHeader(isNew ? 'New Order' : 'Order · ' + draft.tableName, isNew ? fmtD(Date.now()) : 'Active Order', true);

  const safeTables = Array.isArray(selectableTables) && selectableTables.length
    ? selectableTables
    : [{ name: draft.tableName }];

  const total = draft.items.reduce((s, i) => s + i.qty * i.price, 0);
  const itemCount = draft.items.reduce((s, i) => s + i.qty, 0);

  document.getElementById('app').innerHTML = `
    <div class="card">
      <div class="grid-2">
        <div>
          <label>Table ${isNew ? '' : '(Move Table)'}</label>
          <select id="o-table" onchange="draft.tableName=this.value">
            ${safeTables.map(t => `<option value="${esc(t.name)}" ${t.name === draft.tableName ? 'selected' : ''}>${esc(t.name)}</option>`).join('')}
          </select>
        </div>
        <div>
          <label>Customer Name</label>
          <input id="o-cust" placeholder="Walk-in" value="${esc(draft.customerName)}" oninput="draft.customerName=this.value">
        </div>
      </div>
      <label>WhatsApp / Phone (Optional)</label>
      <input id="o-phone" type="tel" placeholder="9876543210" value="${esc(draft.customerPhone)}" oninput="draft.customerPhone=this.value">
    </div>

    <div class="section-title">
      <span>Order Items (<span id="order-count">${itemCount}</span>)</span>
      ${!isNew ? `<span style="color:var(--red);cursor:pointer" onclick="deleteActiveOrder('${draft.id}')">Cancel Order</span>` : ''}
    </div>
    <div class="card" id="order-lines">${renderLines()}</div>

    <div class="section-title"><span>Add Menu Items</span></div>
    <div class="card">
      <input id="menu-search" placeholder="🔍 Search menu items..." value="${esc(menuFilter)}" oninput="filterMenuList(this.value)" style="margin-top:0;margin-bottom:10px">
      <div id="menu-picker">${renderMenuPicker()}</div>
    </div>

    <div class="sticky-dock">
      <div class="dock-top">
        <div>
          <div class="dock-label">RUNNING TOTAL · <span id="dock-items">${itemCount} items</span></div>
          <div class="dock-total" id="dock-total">${money(total)}</div>
        </div>
      </div>
      ${isNew
        ? `<button class="btn btn-primary" style="margin-top:0" onclick="saveNewOrder()">💾 Save & Open Table</button>`
        : `<div class="dock-actions">
             <button class="btn btn-primary" onclick="updateOrder()">🔄 Save Changes</button>
             <button class="btn btn-green" onclick="saveAndSettle('${draft.id}')">✅ Settle Bill</button>
           </div>`
      }
    </div>`;
}

function renderLines() {
  if (draft.items.length === 0) {
    return '<div style="color:var(--ink-muted);font-size:13px;padding:8px 0;text-align:center">Cart is empty — tap items from the menu below.</div>';
  }
  return draft.items.map((it, i) => `
    <div class="item-row">
      <div style="flex:1;min-width:0">
        <div class="iname">${esc(it.name)}</div>
        <div class="iprice">${money(it.price)} × ${it.qty} = <b>${money(it.price * it.qty)}</b></div>
      </div>
      <div class="qty-ctrl">
        <button class="qminus" onclick="chQty(${i}, -1)">−</button>
        <span class="qty-num">${it.qty}</span>
        <button class="qplus" onclick="chQty(${i}, 1)">＋</button>
      </div>
    </div>`).join('');
}

function renderMenuPicker() {
  const q = menuFilter.trim().toLowerCase();
  const visible = cachedMenu.filter(m => m.available !== false && (!q || m.name.toLowerCase().includes(q)));
  if (visible.length === 0) {
    return '<div style="color:var(--ink-muted);font-size:13px;padding:10px 0;text-align:center">No matching items found.</div>';
  }
  return visible.map(m => {
    const inOrder = draft.items.find(x => x.menuId === m.id);
    return `
      <div class="item-row clickable" onclick="addItem(${m.id})">
        <div>
          <div class="iname">${esc(m.name)}</div>
          <div class="iprice">${money(m.price)}</div>
        </div>
        <span class="add-pill ${inOrder ? 'in-cart' : ''}">${inOrder ? `${inOrder.qty} added · +1` : '+ Add'}</span>
      </div>`;
  }).join('');
}

function filterMenuList(val) {
  menuFilter = val;
  const el = document.getElementById('menu-picker');
  if (el) el.innerHTML = renderMenuPicker();
}

function chQty(i, d) {
  draft.items[i].qty += d;
  if (draft.items[i].qty <= 0) draft.items.splice(i, 1);
  refreshForm();
}

function addItem(menuId) {
  const m = cachedMenu.find(x => x.id === menuId);
  if (!m) return;
  const ex = draft.items.find(x => x.menuId === menuId);
  if (ex) ex.qty++;
  else draft.items.push({ menuId: m.id, name: m.name, price: m.price, qty: 1 });
  refreshForm();
}

function refreshForm() {
  const total = draft.items.reduce((s, i) => s + i.qty * i.price, 0);
  const count = draft.items.reduce((s, i) => s + i.qty, 0);
  const linesEl = document.getElementById('order-lines');
  if (linesEl) linesEl.innerHTML = renderLines();
  const pickerEl = document.getElementById('menu-picker');
  if (pickerEl) pickerEl.innerHTML = renderMenuPicker();
  const totEl = document.getElementById('dock-total');
  if (totEl) totEl.textContent = money(total);
  const cntEl = document.getElementById('order-count');
  if (cntEl) cntEl.textContent = count;
  const dockCntEl = document.getElementById('dock-items');
  if (dockCntEl) dockCntEl.textContent = `${count} ${count === 1 ? 'item' : 'items'}`;
}

/* ---------- SAVE / UPDATE / SETTLE ---------- */
async function saveNewOrder() {
  draft.tableName = document.getElementById('o-table').value;
  draft.customerName = document.getElementById('o-cust').value.trim();
  draft.customerPhone = document.getElementById('o-phone').value.trim();
  if (draft.items.length === 0) return showToast('Add at least one item first');

  const o = {
    id: uid(),
    tableName: draft.tableName,
    customerName: draft.customerName,
    customerPhone: draft.customerPhone,
    items: draft.items,
    createdAt: Date.now()
  };
  await putV('active', o);
  showToast(`Order opened for ${o.tableName}`);
  goHome();
}

async function updateOrder(redirectHome = true) {
  draft.tableName = document.getElementById('o-table').value;
  draft.customerName = document.getElementById('o-cust').value.trim();
  draft.customerPhone = document.getElementById('o-phone').value.trim();
  if (draft.items.length === 0) {
    showToast('Order must have at least 1 item');
    return false;
  }
  const o = await getOne('active', draft.id);
  if (!o) {
    goHome();
    return false;
  }
  o.tableName = draft.tableName;
  o.customerName = draft.customerName;
  o.customerPhone = draft.customerPhone;
  o.items = draft.items;
  await putV('active', o);
  if (redirectHome) {
    showToast('Order updated');
    goHome();
  }
  return true;
}

async function saveAndSettle(id) {
  const ok = await updateOrder(false);
  if (ok) goSettle(id);
}

async function deleteActiveOrder(id) {
  if (!confirm('Cancel and delete this active order?')) return;
  await delV('active', id);
  showToast('Order cancelled');
  goHome();
}

async function goSettle(id) {
  const o = await getOne('active', id);
  if (!o) return goHome();
  const total = o.items.reduce((s, i) => s + i.qty * i.price, 0);
  setHeader('Settle Bill', o.tableName, true);

  document.getElementById('app').innerHTML = `
    <div class="receipt">
      <div style="text-align:center;margin-bottom:12px">
        <div style="font-size:18px;font-weight:800">${esc(shop.name)}</div>
        ${shop.phone ? `<div style="font-size:12px;color:var(--ink-muted)">Ph: ${esc(shop.phone)}</div>` : ''}
      </div>
      <div class="list-line"><span style="color:var(--ink-muted)">Table</span><b>${esc(o.tableName)}</b></div>
      <div class="list-line"><span style="color:var(--ink-muted)">Opened</span><span>${fmtD(o.createdAt)} · ${fmtT(o.createdAt)}</span></div>
      ${o.customerName ? `<div class="list-line"><span style="color:var(--ink-muted)">Customer</span><span>${esc(o.customerName)}</span></div>` : ''}
      <div class="receipt-divider"></div>
      ${o.items.map(i => `
        <div class="list-line">
          <span><b>${i.qty}×</b> ${esc(i.name)}</span>
          <span style="font-weight:600">${money(i.qty * i.price)}</span>
        </div>`).join('')}
      <div class="receipt-divider"></div>
      <div class="list-line" style="font-size:19px;font-weight:800;padding-top:4px">
        <span>Grand Total</span>
        <span style="color:var(--primary)">${money(total)}</span>
      </div>
    </div>
    <button class="btn btn-green" style="margin-top:16px" onclick="settle('${o.id}', true)">📲 Settle & Share PDF Bill</button>
    <button class="btn btn-primary" onclick="settle('${o.id}', false)">✅ Settle & Close Table</button>
    <button class="btn btn-gray" onclick="openOrder('${o.id}')">← Back to Order</button>`;
}

async function settle(id, doShare) {
  const o = await getOne('active', id);
  if (!o) return goHome();
  o.settledAt = Date.now();
  o.billNo = shop.billNo || 1;
  shop.billNo = (shop.billNo || 1) + 1;
  await rp(tx('settings', 'readwrite').put(shop, 'shop'));
  await putV('history', o);
  await delV('active', id);
  showToast(`Bill #${o.billNo} settled`);
  if (doShare) {
    try { await sharePdf(o); }
    catch (e) { if (!e || e.name !== 'AbortError') showToast('Share error: ' + e.message); }
  }
  goHome();
}

/* ---------- PDF + WHATSAPP SHARE ---------- */
async function sharePdf(o) {
  if (!window.jspdf) {
    await new Promise((res, rej) => {
      const s = document.createElement('script');
      s.src = 'https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js';
      s.onload = res;
      s.onerror = () => rej(new Error('PDF library failed to load'));
      document.head.appendChild(s);
      setTimeout(() => rej(new Error('PDF load timeout')), 10000);
    });
  }
  const { jsPDF } = window.jspdf;
  const doc = new jsPDF();
  let y = 14;
  doc.setFontSize(16); doc.text(shop.name, 105, y, { align: 'center' }); y += 7;
  if (shop.phone) { doc.setFontSize(10); doc.text('Ph: ' + shop.phone, 105, y, { align: 'center' }); y += 4; }
  doc.setFontSize(9); doc.text('Bill #' + o.billNo + '  |  ' + fmtD(o.settledAt || Date.now()) + ' ' + fmtT(o.settledAt || Date.now()), 105, y, { align: 'center' }); y += 5;
  doc.text('Table: ' + o.tableName + (o.customerName ? '   Customer: ' + o.customerName : ''), 105, y, { align: 'center' }); y += 5;
  doc.line(14, y, 196, y); y += 6;
  doc.setFontSize(10);
  doc.text('Item', 14, y); doc.text('Qty', 120, y, { align: 'right' }); doc.text('Rate', 155, y, { align: 'right' }); doc.text('Amount', 196, y, { align: 'right' }); y += 5;
  doc.line(14, y, 196, y); y += 6;
  o.items.forEach(i => {
    doc.text(String(i.name).slice(0, 40), 14, y);
    doc.text(String(i.qty), 120, y, { align: 'right' });
    doc.text(Number(i.price).toFixed(2), 155, y, { align: 'right' });
    doc.text((i.qty * i.price).toFixed(2), 196, y, { align: 'right' });
    y += 6;
  });
  y += 2; doc.line(14, y, 196, y); y += 7;
  doc.setFontSize(13); doc.setFont(undefined, 'bold');
  doc.text('Total: ' + money(o.items.reduce((s, i) => s + i.qty * i.price, 0)), 196, y, { align: 'right' });

  const blob = doc.output('blob');
  const fname = 'Bill_' + o.billNo + '_' + o.tableName.replace(/\s+/g, '_') + '.pdf';
  const file = new File([blob], fname, { type: 'application/pdf' });
  if (navigator.canShare && navigator.canShare({ files: [file] })) {
    await navigator.share({ files: [file], title: 'Bill #' + o.billNo });
    return;
  }
  const a = document.createElement('a');
  a.href = URL.createObjectURL(blob);
  a.download = fname;
  a.click();
  const ph = (o.customerPhone || '').replace(/\D/g, '');
  window.open('https://wa.me/' + (ph ? (ph.length === 10 ? '91' + ph : ph) : '') + '?text=' + encodeURIComponent('Thank you for dining at ' + shop.name + '! Your Bill #' + o.billNo + ' total is ' + money(o.items.reduce((s, i) => s + i.qty * i.price, 0))), '_blank');
}

/* ---------- HISTORY ---------- */
async function goHistory() {
  setNav('nav-history');
  setHeader('Settled Bills', shop ? shop.name : '', false);
  const h = await getAll('history');
  h.sort((a, b) => b.settledAt - a.settledAt);

  document.getElementById('app').innerHTML = h.length === 0
    ? `<div class="empty-state"><div class="face">🧾</div><p>No settled bills yet</p><span>Completed table bills will appear here</span></div>`
    : h.map(o => {
        const tot = o.items.reduce((s, i) => s + i.qty * i.price, 0);
        return `
          <div class="card" style="cursor:pointer" onclick="viewBill('${o.id}')">
            <div class="row-flex" style="justify-content:space-between">
              <div>
                <div style="font-weight:800;font-size:15px">Bill #${o.billNo} · <span style="color:var(--primary)">${esc(o.tableName)}</span></div>
                <div style="font-size:12px;color:var(--ink-muted);margin-top:3px">${fmtD(o.settledAt)} · ${fmtT(o.settledAt)}${o.customerName ? ' · ' + esc(o.customerName) : ''}</div>
              </div>
              <div style="text-align:right">
                <div style="font-weight:800;font-size:16px;color:var(--ink)">${money(tot)}</div>
                <div style="font-size:11px;color:var(--green);font-weight:700">PAID</div>
              </div>
            </div>
          </div>`;
      }).join('');
}

async function viewBill(id) {
  const o = await getOne('history', id);
  if (!o) return goHistory();
  setHeader('Bill #' + o.billNo, fmtD(o.settledAt), false);
  document.getElementById('app').innerHTML = `
    <div class="receipt">
      <div class="list-line"><span style="color:var(--ink-muted)">Table</span><b>${esc(o.tableName)}</b></div>
      <div class="list-line"><span style="color:var(--ink-muted)">Settled At</span><span>${fmtD(o.settledAt)} ${fmtT(o.settledAt)}</span></div>
      ${o.customerName ? `<div class="list-line"><span style="color:var(--ink-muted)">Customer</span><span>${esc(o.customerName)} ${o.customerPhone ? '(' + esc(o.customerPhone) + ')' : ''}</span></div>` : ''}
      <div class="receipt-divider"></div>
      ${o.items.map(i => `<div class="list-line"><span><b>${i.qty}×</b> ${esc(i.name)}</span><span>${money(i.qty * i.price)}</span></div>`).join('')}
      <div class="receipt-divider"></div>
      <div class="list-line" style="font-size:18px;font-weight:800">
        <span>Total Paid</span>
        <span style="color:var(--primary)">${money(o.items.reduce((s, i) => s + i.qty * i.price, 0))}</span>
      </div>
    </div>
    <button class="btn btn-green" style="margin-top:14px" onclick="reshare('${o.id}')">📤 Resend PDF Receipt</button>
    <button class="btn btn-gray" onclick="goHistory()">← Back to History</button>`;
}

async function reshare(id) {
  const o = await getOne('history', id);
  try { await sharePdf(o); }
  catch (e) { if (e && e.name !== 'AbortError') showToast(e.message); }
}

/* ---------- MENU MANAGER ---------- */
async function loadMenuCache() { cachedMenu = await getAll('menu'); }

async function goMenu() {
  setNav('nav-menu');
  setHeader('Menu Catalog', cachedMenu.length + ' items', false);
  await loadMenuCache();

  document.getElementById('app').innerHTML = `
    <div class="card">
      <label>Add New Dish / Item</label>
      <div class="row-flex" style="margin-top:6px">
        <input id="m-name" placeholder="e.g. Masala Dosa" style="margin-top:0" onkeydown="if(event.key==='Enter')addMenuItem()">
        <input id="m-price" type="number" step="0.01" placeholder="Price" style="margin-top:0;width:115px" onkeydown="if(event.key==='Enter')addMenuItem()">
      </div>
      <button class="btn btn-primary" onclick="addMenuItem()">＋ Add to Menu</button>
    </div>

    <div class="section-title"><span>Menu Items (${cachedMenu.length})</span></div>
    ${cachedMenu.length === 0
      ? `<div class="empty-state"><div class="face">🍽️</div><p>Menu is empty</p><span>Add your dishes above to begin billing</span></div>`
      : cachedMenu.map(m => `
        <div class="card" style="${m.available === false ? 'opacity:0.68' : ''}">
          <div class="row-flex" style="justify-content:space-between">
            <div>
              <div style="font-weight:700;font-size:15px">${esc(m.name)} — <span style="color:var(--primary)">${money(m.price)}</span></div>
              <div style="font-size:12px;color:${m.available === false ? 'var(--amber)' : 'var(--green)'};font-weight:600;margin-top:2px">
                ${m.available === false ? '⏸ Hidden from POS' : '● Available'}
              </div>
            </div>
            <div class="row-flex">
              <button class="btn btn-gray btn-sm" onclick="toggleItem(${m.id})">${m.available === false ? 'Show' : 'Hide'}</button>
              <button class="btn btn-red btn-sm" onclick="delItem(${m.id})">✕</button>
            </div>
          </div>
          <div class="row-flex" style="margin-top:10px;padding-top:10px;border-top:1px solid #f1f5f9">
            <input id="e-name-${m.id}" value="${esc(m.name)}" style="margin-top:0;padding:8px 10px;font-size:14px">
            <input id="e-price-${m.id}" type="number" step="0.01" value="${m.price}" style="margin-top:0;width:96px;padding:8px 10px;font-size:14px">
            <button class="btn btn-primary btn-sm" onclick="editItem(${m.id})">Save</button>
          </div>
        </div>`).join('')}`;
}

async function addMenuItem() {
  const n = document.getElementById('m-name').value.trim();
  const p = parseFloat(document.getElementById('m-price').value);
  if (!n || !(p >= 0)) return showToast('Enter valid item name & price');
  await putV('menu', { name: n, price: p, available: true });
  showToast('Item added');
  goMenu();
}

async function editItem(id) {
  const n = document.getElementById('e-name-' + id).value.trim();
  const p = parseFloat(document.getElementById('e-price-' + id).value);
  if (!n || !(p >= 0)) return showToast('Enter valid name & price');
  const m = await getOne('menu', id);
  m.name = n; m.price = p;
  await putV('menu', m);
  showToast('Item updated');
  goMenu();
}

async function toggleItem(id) {
  const m = await getOne('menu', id);
  m.available = m.available === false ? true : false;
  await putV('menu', m);
  goMenu();
}

async function delItem(id) {
  if (!confirm('Delete this item from menu?')) return;
  await delV('menu', id);
  showToast('Item deleted');
  goMenu();
}

/* ---------- SALES & EXPORT CSV ---------- */
async function goExport() {
  setNav('nav-export');
  setHeader('Sales & Reports', 'CSV Export', false);

  const h = await getAll('history');
  const todayStart = new Date(); todayStart.setHours(0, 0, 0, 0);
  const todayBills = h.filter(o => o.settledAt >= todayStart.getTime());
  const todayRev = todayBills.reduce((sum, o) => sum + o.items.reduce((s, i) => s + i.qty * i.price, 0), 0);

  document.getElementById('app').innerHTML = `
    <div class="kpi-row">
      <div class="kpi-card">
        <div class="kpi-label">Today's Bills</div>
        <div class="kpi-val">${todayBills.length}</div>
      </div>
      <div class="kpi-card">
        <div class="kpi-label">Today's Revenue</div>
        <div class="kpi-val accent">${money(todayRev)}</div>
      </div>
    </div>
    <div class="card">
      <div style="font-weight:700;font-size:15px;margin-bottom:4px">Download Sales Spreadsheet (CSV)</div>
      <div style="font-size:13px;color:var(--ink-muted);margin-bottom:12px">Compatible with Excel, Google Sheets, and accounting tools.</div>
      <button class="btn btn-primary" onclick="exportCSV('today')">📅 Export Today's Sales</button>
      <button class="btn btn-gray" onclick="exportCSV('week')">📆 Export Last 7 Days</button>
      <button class="btn btn-gray" onclick="exportCSV('month')">🗓 Export This Month</button>
    </div>`;
}

async function exportCSV(range) {
  const h = await getAll('history');
  const now = new Date();
  const start = new Date(now);
  let label;
  if (range === 'today') { start.setHours(0, 0, 0, 0); label = 'today'; }
  else if (range === 'week') { start.setDate(start.getDate() - 6); start.setHours(0, 0, 0, 0); label = '7days'; }
  else { start.setDate(1); start.setHours(0, 0, 0, 0); label = 'month'; }

  const rows = h.filter(o => o.settledAt >= start.getTime()).sort((a, b) => a.settledAt - b.settledAt);
  if (rows.length === 0) return showToast('No bills in selected period');

  let csv = 'Bill No,Date,Time,Table,Customer,Phone,Items,Total\n';
  rows.forEach(o => {
    csv += [
      o.billNo,
      fmtD(o.settledAt),
      fmtT(o.settledAt),
      '"' + o.tableName.replace(/"/g, '""') + '"',
      '"' + String(o.customerName || '').replace(/"/g, '""') + '"',
      String(o.customerPhone || ''),
      '"' + o.items.map(i => i.name + ' x' + i.qty).join('; ').replace(/"/g, '""') + '"',
      o.items.reduce((s, i) => s + i.qty * i.price, 0).toFixed(2)
    ].join(',') + '\n';
  });

  const a = document.createElement('a');
  a.href = URL.createObjectURL(new Blob([csv], { type: 'text/csv' }));
  a.download = 'sales_' + label + '_' + start.getDate() + '-' + (start.getMonth() + 1) + '-' + start.getFullYear() + '.csv';
  a.click();
  showToast('Exported ' + rows.length + ' bills');
}

/* ---------- SETTINGS ---------- */
async function goSettings() {
  renderSetup();
}

/* ---------- INIT ---------- */
(async function init() {
  try {
    await openDB();
    if (navigator.storage && navigator.storage.persist) navigator.storage.persist().catch(() => {});
    shop = (await getOne('settings', 'shop')) || null;
    await loadMenuCache();
    if (!shop || !shop.done) renderSetup();
    else goHome();
  } catch (e) {
    document.getElementById('app').innerHTML = '<div class="empty-state"><p>Storage error: ' + esc(e.message) + '</p></div>';
  }
})();
</script>
</body>
</html>
