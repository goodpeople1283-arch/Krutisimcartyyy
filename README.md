<!DOCTYPE html>
<html lang="ru">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Генератор СИМ-карт</title>
<style>
  * { box-sizing: border-box; margin: 0; padding: 0; }
  body {
    font-family: 'Segoe UI', Arial, sans-serif;
    background: linear-gradient(135deg, #1a1a2e 0%, #16213e 100%);
    color: #eee;
    min-height: 100vh;
    padding: 15px;
  }
  h1 { text-align: center; font-size: 26px; margin-bottom: 10px; color: #4fc3f7; }
  .container { max-width: 1100px; margin: 0 auto; }
  .panel {
    background: rgba(255,255,255,0.06);
    border: 1px solid rgba(255,255,255,0.15);
    border-radius: 12px;
    padding: 15px;
    margin-bottom: 15px;
  }
  .balance {
    text-align: center;
    font-size: 22px;
    font-weight: bold;
    color: #ffd54f;
    margin-bottom: 12px;
  }
  button {
    cursor: pointer;
    border: none;
    border-radius: 8px;
    padding: 10px 16px;
    font-size: 15px;
    font-weight: bold;
    transition: transform 0.1s, filter 0.2s;
    color: #fff;
  }
  button:hover:not(:disabled) { filter: brightness(1.15); }
  button:active:not(:disabled) { transform: scale(0.97); }
  button:disabled { opacity: 0.45; cursor: not-allowed; }
  .btn-main {
    display: block;
    width: 100%;
    font-size: 20px;
    padding: 16px;
    background: linear-gradient(90deg, #ff5722, #ff9800);
    margin-bottom: 10px;
  }
  .btn-blue { background: #1976d2; }
  .btn-green { background: #2e7d32; }
  .btn-red { background: #c62828; }
  .btn-gray { background: #555; }
  .btn-purple { background: #7b1fa2; }

  .tabs { display: flex; gap: 8px; margin-bottom: 12px; flex-wrap: wrap; }
  .tab {
    flex: 1;
    min-width: 120px;
    background: #333;
    color: #ccc;
    padding: 10px;
  }
  .tab.active { background: #1976d2; color: #fff; }

  .card-grid {
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(240px, 1fr));
    gap: 10px;
  }
  .card {
    background: rgba(255,255,255,0.08);
    border-radius: 10px;
    padding: 12px;
    border-left: 4px solid #555;
  }
  .card .title { font-weight: bold; font-size: 16px; margin-bottom: 4px; }
  .card .sub { font-size: 13px; color: #aaa; margin-bottom: 8px; }
  .card .price { color: #ffd54f; font-weight: bold; margin-bottom: 8px; }
  .card button { width: 100%; padding: 8px; font-size: 14px; }

  .log {
    height: 130px;
    overflow-y: auto;
    font-size: 13px;
    line-height: 1.5;
    background: rgba(0,0,0,0.3);
    border-radius: 8px;
    padding: 10px;
  }
  .log div { border-bottom: 1px solid rgba(255,255,255,0.06); padding: 2px 0; }
  .log .green { color: #81c784; }
  .log .red { color: #e57373; }
  .log .yellow { color: #ffd54f; }

  .op-badge {
    display: inline-block;
    padding: 3px 10px;
    border-radius: 20px;
    font-size: 13px;
    font-weight: bold;
    margin: 2px;
    color: #fff;
  }
  .op-rtk { background: #6a1b9a; }
  .op-yota { background: #e91e63; }
  .op-tmobile { background: #0088cc; }
  .op-mts { background: #e30611; }
  .op-megafon { background: #00a651; }
  .op-beeline { background: #ffc107; color: #000; }
  .op-t2 { background: #00bcd4; color: #000; }

  .sim-item {
    display: flex;
    align-items: center;
    justify-content: space-between;
    gap: 8px;
    background: rgba(255,255,255,0.07);
    border-radius: 8px;
    padding: 8px 10px;
    margin-bottom: 6px;
    flex-wrap: wrap;
  }
  .sim-item .ops { flex: 1; }
  .sim-item button { padding: 6px 12px; font-size: 13px; }

  .phone-item {
    background: rgba(255,255,255,0.07);
    border-radius: 8px;
    padding: 10px;
    margin-bottom: 8px;
  }
  .phone-item .row {
    display: flex;
    justify-content: space-between;
    align-items: center;
    gap: 8px;
    flex-wrap: wrap;
  }
  .phone-item .name { font-weight: bold; }
  .phone-item .detail { font-size: 13px; color: #aaa; margin-top: 4px; }
  .phone-item .value { color: #81c784; font-weight: bold; }
  .phone-item button { padding: 6px 12px; font-size: 13px; margin-top: 6px; }
  .empty { text-align: center; color: #777; padding: 20px; font-size: 14px; }
  .hint { font-size: 12px; color: #888; margin-bottom: 10px; }
  .scroll-box { max-height: 420px; overflow-y: auto; padding-right: 4px; }
</style>
</head>
<body>
<div class="container">
  <h1>📱 Генератор СИМ-карт</h1>

  <div class="balance">💰 Баланс: <span id="balance">0</span> ₽</div>

  <!-- Генерация сим -->
  <div class="panel">
    <button class="btn-main" id="spinBtn">🎲 Крутить СИМ-карту (2 000 ₽)</button>
    <div class="hint">Каждая прокрутка стоит 2 000 ₽ и даёт случайного российского оператора.</div>
  </div>

  <!-- Вкладки -->
  <div class="tabs">
    <button class="tab active" data-tab="sims">📶 Мои СИМ-карты</button>
    <button class="tab" data-tab="shop">🛒 Магазин телефонов</button>
    <button class="tab" data-tab="myphones">📱 Мои телефоны</button>
    <button class="tab" data-tab="log">📜 Журнал</button>
  </div>

  <!-- СИМ-карты -->
  <div class="panel tab-content" id="tab-sims">
    <div class="hint">Нажмите «Вставить в телефон» у СИМ-карты, затем выберите телефон.</div>
    <div id="simsList"></div>
  </div>

  <!-- Магазин -->
  <div class="panel tab-content" id="tab-shop" style="display:none">
    <div class="hint">Покупка телефона по фиксированной цене редкости. Модель телефона выбирается случайно.</div>
    <div class="card-grid" id="shopGrid"></div>
  </div>

  <!-- Мои телефоны -->
  <div class="panel tab-content" id="tab-myphones" style="display:none">
    <div class="hint">Вставьте СИМ-карту в телефон и продайте его дороже! Цена продажи = базовая цена модели + 5% за каждую СИМ-карту (макс. +35%).</div>
    <div id="phonesList"></div>
  </div>

  <!-- Журнал -->
  <div class="panel tab-content" id="tab-log" style="display:none">
    <div class="log" id="log"></div>
  </div>
</div>

<script>
// ================= ДАННЫЕ =================
const SPIN_COST = 2000;
const START_BALANCE = 10000;

const OPERATORS = [
  { id: 'rtk', name: 'Ростелеком', cls: 'op-rtk' },
  { id: 'yota', name: 'Yota', cls: 'op-yota' },
  { id: 'tmobile', name: 'Т-Мобайл', cls: 'op-tmobile' },
  { id: 'mts', name: 'МТС', cls: 'op-mts' },
  { id: 'megafon', name: 'МегаФон', cls: 'op-megafon' },
  { id: 'beeline', name: 'Билайн', cls: 'op-beeline' },
  { id: 't2', name: 't2', cls: 'op-t2' }
];

const RARITIES = [
  { name: 'Сломанная',     icon: '⚫', chance: 30,   min: 1500,      max: 4000,       shop: 1000 },
  { name: 'Обычная',       icon: '⚪', chance: 25,   min: 5000,      max: 12000,      shop: 4000 },
  { name: 'Необычная',     icon: '🟢', chance: 18,   min: 20000,     max: 50000,      shop: 15000 },
  { name: 'Редкая',        icon: '🔵', chance: 12,   min: 80000,     max: 200000,     shop: 60000 },
  { name: 'Очень редкая',  icon: '🩵', chance: 7,    min: 300000,    max: 700000,     shop: 250000 },
  { name: 'Эпическая',     icon: '🟣', chance: 4,    min: 1000000,   max: 2500000,    shop: 800000 },
  { name: 'Легендарная',   icon: '🟡', chance: 2,    min: 5000000,   max: 12000000,   shop: 4000000 },
  { name: 'Мифическая',    icon: '🔴', chance: 1,    min: 20000000,  max: 50000000,   shop: 15000000 },
  { name: 'Божественная',  icon: '🌈', chance: 0.7,  min: 100000000, max: 250000000,  shop: 80000000 },
  { name: 'Космическая',   icon: '✨', chance: 0.3,  min: 800000000, max: 2000000000, shop: 600000000 }
];

const PHONE_MODELS = [
  { name: 'Realme Note 50', price: 8000 },
  { name: 'Realme C63', price: 10000 },
  { name: 'Realme Narzo 70', price: 15000 },
  { name: 'Realme 12', price: 20000 },
  { name: 'Realme 12+', price: 28000 },
  { name: 'Realme 13 Pro+', price: 45000 },
  { name: 'Realme GT6', price: 52000 },
  { name: 'Realme GT7', price: 65000 },
  { name: 'OnePlus Nord 4', price: 42000 },
  { name: 'OnePlus 12R', price: 60000 },
  { name: 'OnePlus 12', price: 85000 },
  { name: 'OnePlus 13', price: 95000 },
  { name: 'Honor X6b', price: 10000 },
  { name: 'Honor Play 60', price: 12000 },
  { name: 'Honor X8b', price: 20000 },
  { name: 'Honor X9c', price: 35000 },
  { name: 'Honor 200', price: 50000 },
  { name: 'Honor 200 Pro', price: 70000 },
  { name: 'Honor Magic6', price: 95000 },
  { name: 'Honor Magic7', price: 110000 },
  { name: 'Honor Magic V3', price: 150000 },
  { name: 'Huawei Nova 12i', price: 30000 },
  { name: 'Huawei Pura 70', price: 75000 },
  { name: 'Huawei Mate 70', price: 95000 },
  { name: 'Huawei Pura 70 Ultra', price: 140000 },
  { name: 'Huawei Mate X5', price: 180000 },
  { name: 'Vivo Y03', price: 7000 },
  { name: 'Vivo Y28', price: 13000 },
  { name: 'Vivo V40', price: 45000 },
  { name: 'Vivo X100', price: 90000 },
  { name: 'Vivo X200', price: 100000 },
  { name: 'Vivo X Fold3 Pro', price: 160000 },
  { name: 'Oppo A60', price: 15000 },
  { name: 'Oppo Reno12', price: 45000 },
  { name: 'Oppo Find X8', price: 85000 },
  { name: 'Oppo Find N3', price: 155000 },
  { name: 'Google Pixel 7a', price: 45000 },
  { name: 'Google Pixel 8a', price: 60000 },
  { name: 'Google Pixel 8', price: 75000 },
  { name: 'Google Pixel 9', price: 85000 },
  { name: 'Google Pixel 8 Pro', price: 100000 },
  { name: 'Google Pixel 9 Pro', price: 120000 },
  { name: 'Google Pixel 10', price: 95000 },
  { name: 'Google Pixel 9 Pro XL', price: 135000 },
  { name: 'Google Pixel Fold', price: 140000 },
  { name: 'Google Pixel 10 Pro', price: 130000 },
  { name: 'Nothing CMF Phone 1', price: 25000 },
  { name: 'Nothing Phone 2a', price: 35000 },
  { name: 'Nothing Phone 3a', price: 40000 },
  { name: 'Nothing Phone 2', price: 60000 },
  { name: 'Nothing Phone 3', price: 75000 },
  { name: 'Asus Zenfone 11 Ultra', price: 90000 },
  { name: 'Asus ROG Phone 8', price: 110000 },
  { name: 'Asus ROG Phone 9', price: 130000 },
  { name: 'Asus ROG Phone 9 Pro', price: 150000 },
  { name: 'Sony Xperia 10 VI', price: 55000 },
  { name: 'Sony Xperia 1 VI', price: 130000 },
  { name: 'Motorola G04', price: 8000 },
  { name: 'Motorola G85', price: 25000 },
  { name: 'Edge 50 Neo', price: 40000 },
  { name: 'Edge 50 Pro', price: 60000 },
  { name: 'Edge 50 Ultra', price: 75000 },
  { name: 'Razr 50', price: 80000 },
  { name: 'Razr 50 Ultra', price: 115000 },
  { name: 'Nokia 105 (кнопочный)', price: 2000 },
  { name: 'Nokia 3310', price: 4000 },
  { name: 'Nokia C32', price: 10000 },
  { name: 'Nokia G42', price: 15000 },
  { name: 'Nokia X30', price: 40000 },
  { name: 'Tecno Spark 20', price: 9000 },
  { name: 'Tecno Spark 30', price: 11000 },
  { name: 'Tecno Camon 30', price: 18000 },
  { name: 'Tecno Pova 6 Pro', price: 20000 },
  { name: 'Tecno Phantom X2', price: 35000 },
  { name: 'Tecno Phantom V Fold', price: 100000 },
  { name: 'Infinix Hot 40i', price: 8000 },
  { name: 'Infinix Note 40', price: 15000 },
  { name: 'Infinix Note 40 Pro', price: 20000 },
  { name: 'Infinix GT 20 Pro', price: 30000 },
  { name: 'Infinix Zero 40', price: 35000 },
  { name: 'Nubia Focus', price: 20000 },
  { name: 'RedMagic 9 Pro', price: 70000 },
  { name: 'Nubia Z60 Ultra', price: 80000 },
  { name: 'RedMagic 10 Pro', price: 85000 },
  { name: 'CAT S53 (защищённый)', price: 35000 },
  { name: 'CAT S75 (защищённый)', price: 60000 },
  { name: 'Redmi A2', price: 7000 },
  { name: 'Poco C61', price: 7000 },
  { name: 'Redmi 14C', price: 9000 },
  { name: 'Redmi 13C', price: 10000 },
  { name: 'Redmi Note 13', price: 15000 },
  { name: 'Redmi Note 14', price: 17000 },
  { name: 'Poco M6', price: 12000 },
  { name: 'Redmi Note 13 Pro', price: 25000 },
  { name: 'Redmi Note 14 Pro', price: 28000 },
  { name: 'Poco X6 Pro', price: 30000 },
  { name: 'Poco X7', price: 30000 },
  { name: 'Poco X7 Pro', price: 36000 },
  { name: 'Redmi Note 14 Pro+', price: 40000 },
  { name: 'Xiaomi 13T', price: 55000 },
  { name: 'Poco F6', price: 45000 },
  { name: 'Xiaomi 14', price: 75000 },
  { name: 'Xiaomi 15', price: 90000 },
  { name: 'Poco F7', price: 50000 },
  { name: 'Poco F7 Ultra', price: 70000 },
  { name: 'Xiaomi 14 Ultra', price: 130000 },
  { name: 'Xiaomi Mix Flip', price: 115000 },
  { name: 'Xiaomi 15 Ultra', price: 155000 },
  { name: 'Galaxy A05', price: 9000 },
  { name: 'Galaxy A06', price: 11000 },
  { name: 'Galaxy A15', price: 15000 },
  { name: 'Galaxy A25', price: 20000 },
  { name: 'Galaxy M55', price: 25000 },
  { name: 'Galaxy A35', price: 28000 },
  { name: 'Galaxy A55', price: 42000 },
  { name: 'Galaxy A56', price: 45000 },
  { name: 'Galaxy S23 FE', price: 45000 },
  { name: 'Galaxy S23', price: 70000 },
  { name: 'Galaxy S24', price: 80000 },
  { name: 'Galaxy S25', price: 90000 },
  { name: 'Galaxy S24+', price: 95000 },
  { name: 'Galaxy S25+', price: 105000 },
  { name: 'Galaxy Z Flip6', price: 100000 },
  { name: 'Galaxy S24 Ultra', price: 120000 },
  { name: 'Galaxy Z Flip7', price: 110000 },
  { name: 'Galaxy S25 Ultra', price: 130000 },
  { name: 'Galaxy Z Fold6', price: 165000 },
  { name: 'Galaxy Z Fold7', price: 190000 },
  { name: 'iPhone SE (2022)', price: 35000 },
  { name: 'iPhone 11', price: 40000 },
  { name: 'iPhone 12', price: 50000 },
  { name: 'iPhone 13', price: 60000 },
  { name: 'iPhone 14', price: 70000 },
  { name: 'iPhone 14 Plus', price: 80000 },
  { name: 'iPhone 15', price: 75000 },
  { name: 'iPhone 15 Plus', price: 90000 },
  { name: 'iPhone 14 Pro', price: 105000 },
  { name: 'iPhone 15 Pro', price: 115000 },
  { name: 'iPhone 16', price: 85000 },
  { name: 'iPhone 16 Plus', price: 95000 },
  { name: 'iPhone 17 Air', price: 115000 },
  { name: 'iPhone 14 Pro Max', price: 120000 },
  { name: 'iPhone 15 Pro Max', price: 135000 },
  { name: 'iPhone 16 Pro', price: 130000 },
  { name: 'iPhone 17', price: 90000 },
  { name: 'iPhone 16 Pro Max', price: 150000 },
  { name: 'iPhone 17 Pro', price: 140000 },
  { name: 'iPhone 17 Pro Max', price: 160000 }
];

// ================= СОСТОЯНИЕ =================
let balance = START_BALANCE;
let simIdCounter = 1;
let phoneIdCounter = 1;
let sims = [];
let phones = [];

// ================= УТИЛИТЫ =================
const fmt = n => n.toLocaleString('ru-RU');

function log(msg, cls = '') {
  const logBox = document.getElementById('log');
  const div = document.createElement('div');
  div.className = cls;
  div.textContent = msg;
  logBox.prepend(div);
  while (logBox.children.length > 100) logBox.lastChild.remove();
}

function getOperator(id) {
  return OPERATORS.find(o => o.id === id);
}

function randomOperator() {
  return OPERATORS[Math.floor(Math.random() * OPERATORS.length)];
}

// ================= ОТРИСОВКА =================
function renderBalance() {
  document.getElementById('balance').textContent = fmt(balance);
  // Обновляем доступность кнопки прокрутки
  const btn = document.getElementById('spinBtn');
  if (balance < SPIN_COST) {
    btn.textContent = `🎲 Крутить СИМ-карту (2 000 ₽) — не хватает денег`;
  } else {
    btn.textContent = `🎲 Крутить СИМ-карту (2 000 ₽)`;
  }
}

function renderSims() {
  const box = document.getElementById('simsList');
  if (sims.length === 0) {
    box.innerHTML = '<div class="empty">У вас пока нет СИМ-карт. Крутите генератор!</div>';
    return;
  }
  box.innerHTML = sims.map(sim => {
    const op = getOperator(sim.operatorId);
    const inPhone = phones.find(p => p.sims.includes(sim.id));
    const status = inPhone ? `в ${inPhone.model}` : 'свободна';
    const disabled = inPhone ? 'disabled' : '';
    return `<div class="sim-item">
      <div class="ops">
        <span class="op-badge ${op.cls}">${op.name}</span>
        <span style="color:#888;font-size:12px;">#${sim.id} · ${status}</span>
      </div>
      <button class="btn-green" ${disabled} onclick="openInsertSim(${sim.id})">Вставить в телефон</button>
    </div>`;
  }).join('');
}

function renderShop() {
  const grid = document.getElementById('shopGrid');
  grid.innerHTML = RARITIES.map((r, i) => `
    <div class="card" style="border-left-color:${rarityColor(i)}">
      <div class="title">${r.icon} ${r.name}</div>
      <div class="sub">Шанс: ${r.chance}%</div>
      <div class="price">Цена в магазине: ${fmt(r.shop)} ₽</div>
      <button class="btn-purple" onclick="buyPhone(${i})">Купить</button>
    </div>
  `).join('');
}

function rarityColor(idx) {
  const colors = ['#777','#bbb','#4caf50','#2196f3','#26c6da','#9c27b0','#ffeb3b','#f44336','#e91e63','#00e5ff'];
  return colors[idx] || '#555';
}

function simBonusText(phone) {
  if (phone.sims.length === 0) return 'нет СИМ-карт';
  return phone.sims.map(id => {
    const s = sims.find(x => x.id === id);
    return s ? getOperator(s.operatorId).name : '?';
  }).join(', ');
}

function calcSellPrice(phone) {
  const bonusPerSim = 0.05;
  const maxBonus = 0.35;
  const bonus = Math.min(phone.sims.length * bonusPerSim, maxBonus);
  return Math.round(phone.basePrice * (1 + bonus));
}

function renderPhones() {
  const box = document.getElementById('phonesList');
  if (phones.length === 0) {
    box.innerHTML = '<div class="empty">У вас пока нет телефонов. Купите в магазине!</div>';
    return;
  }
  box.innerHTML = phones.map(p => {
    const sellPrice = calcSellPrice(p);
    return `<div class="phone-item">
      <div class="row">
        <div class="name">${p.model}</div>
        <div style="color:#888;font-size:12px;">#${p.id}</div>
      </div>
      <div class="detail">Редкость: ${p.rarity.icon} ${p.rarity.name}</div>
      <div class="detail">Базовая цена модели: ${fmt(p.basePrice)} ₽</div>
      <div class="detail">СИМ-карты: ${simBonusText(p)}</div>
      <div class="detail">Цена продажи: <span class="value">${fmt(sellPrice)} ₽</span></div>
      <div style="display:flex;gap:8px;flex-wrap:wrap;margin-top:6px;">
        <button class="btn-blue" onclick="openInsertSimToPhone(${p.id})">Вставить СИМ</button>
        ${p.sims.length > 0 ? `<button class="btn-gray" onclick="removeSimsFromPhone(${p.id})">Вынуть СИМ</button>` : ''}
        <button class="btn-green" onclick="sellPhone(${p.id})">Продать за ${fmt(sellPrice)} ₽</button>
      </div>
    </div>`;
  }).join('');
}

function renderAll() {
  renderBalance();
  renderSims();
  renderShop();
  renderPhones();
}

// ================= ДЕЙСТВИЯ =================
function spinSim() {
  if (balance < SPIN_COST) {
    log(`❌ Недостаточно денег для прокрутки (нужно ${fmt(SPIN_COST)} ₽)`, 'red');
    return;
  }
  balance -= SPIN_COST;
  const op = randomOperator();
  const sim = { id: simIdCounter++, operatorId: op.id };
  sims.push(sim);
  log(`🎲 Выпала СИМ-карта: ${op.name} (#${sim.id}) — списано ${fmt(SPIN_COST)} ₽`, 'yellow');
  renderAll();
}

function buyPhone(rarityIdx) {
  const r = RARITIES[rarityIdx];
  if (balance < r.shop) {
    log(`❌ Недостаточно денег для покупки «${r.name}» (нужно ${fmt(r.shop)} ₽)`, 'red');
    return;
  }
  balance -= r.shop;
  const model = PHONE_MODELS[Math.floor(Math.random() * PHONE_MODELS.length)];
  const phone = {
    id: phoneIdCounter++,
    model: model.name,
    basePrice: model.price,
    rarity: r,
    sims: []
  };
  phones.push(phone);
  log(`🛒 Куплен телефон ${model.name} (${r.icon} ${r.name}) за ${fmt(r.shop)} ₽`, 'green');
  renderAll();
}

function sellPhone(phoneId) {
  const idx = phones.findIndex(p => p.id === phoneId);
  if (idx === -1) return;
  const phone = phones[idx];
  const price = calcSellPrice(phone);
  balance += price;
  phones.splice(idx, 1);
  log(`💰 Продан ${phone.model} за ${fmt(price)} ₽`, 'green');
  renderAll();
}

function openInsertSim(simId) {
  const sim = sims.find(s => s.id === simId);
  if (!sim) return;
  if (phones.length === 0) {
    log('❌ Нет телефонов. Сначала купите телефон в магазине!', 'red');
    return;
  }
  const op = getOperator(sim.operatorId);
  const choice = prompt(
    `Выберите телефон для СИМ-карты ${op.name} (#${sim.id}).\nВведите номер телефона из списка:\n\n` +
    phones.map(p => `#${p.id} — ${p.model} (СИМ: ${p.sims.length})`).join('\n')
  );
  if (!choice) return;
  const phoneId = parseInt(choice.replace('#', '').trim());
  const phone = phones.find(p => p.id === phoneId);
  if (!phone) {
  
