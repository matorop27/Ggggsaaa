<!DOCTYPE html>
<html lang="ru">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>🦆 Duck Casino — Утиное Казино</title>
<style>
  * { margin: 0; padding: 0; box-sizing: border-box; }

  :root {
    --bg: #0e1117;
    --panel: #161b22;
    --panel-2: #1f2630;
    --accent: #f5c542;
    --accent-2: #ffb700;
    --green: #2ecc71;
    --red: #e74c3c;
    --text: #e6edf3;
    --muted: #8b949e;
  }

  body {
    font-family: 'Segoe UI', system-ui, -apple-system, sans-serif;
    background: radial-gradient(circle at 50% 0%, #1a2230 0%, var(--bg) 60%);
    color: var(--text);
    min-height: 100vh;
    display: flex;
    justify-content: center;
    align-items: flex-start;
    padding: 20px;
    overflow-x: hidden;
  }

  #app {
    width: 100%;
    max-width: 1150px;
    display: flex;
    flex-direction: column;
    gap: 16px;
  }

  /* ===== Шапка ===== */
  .topbar {
    display: flex;
    align-items: center;
    justify-content: space-between;
    background: var(--panel);
    border: 1px solid #2a3441;
    border-radius: 14px;
    padding: 14px 22px;
    box-shadow: 0 4px 20px rgba(0,0,0,.4);
  }

  .logo {
    font-size: 22px;
    font-weight: 800;
    letter-spacing: 1px;
    background: linear-gradient(90deg, var(--accent), #ffe58a);
    -webkit-background-clip: text;
    background-clip: text;
    -webkit-text-fill-color: transparent;
  }

  .balance-box {
    display: flex;
    align-items: center;
    gap: 8px;
    background: var(--panel-2);
    padding: 8px 18px;
    border-radius: 10px;
    border: 1px solid #2f3a48;
    font-size: 20px;
    font-weight: 700;
  }

  .balance-label { color: var(--muted); font-size: 14px; font-weight: 500; }
  .balance-value { color: var(--accent); min-width: 60px; text-align: right; transition: color .2s; }
  .balance-value.flash-win { color: var(--green); }
  .balance-value.flash-lose { color: var(--red); }
  .coin { font-size: 18px; }

  .reset-btn {
    background: var(--panel-2);
    border: 1px solid #2f3a48;
    color: var(--muted);
    width: 42px;
    height: 42px;
    border-radius: 10px;
    font-size: 20px;
    cursor: pointer;
    transition: .2s;
  }
  .reset-btn:hover { color: var(--accent); border-color: var(--accent); transform: rotate(90deg); }

  /* ===== Раскладка ===== */
  .layout {
    display: grid;
    grid-template-columns: 260px 1fr 220px;
    gap: 16px;
    align-items: start;
  }

  @media (max-width: 950px) {
    .layout { grid-template-columns: 1fr; }
  }

  /* ===== Панель ставки ===== */
  .bet-panel, .sidebar {
    background: var(--panel);
    border: 1px solid #2a3441;
    border-radius: 14px;
    padding: 18px;
    display: flex;
    flex-direction: column;
    gap: 14px;
  }

  .bet-panel h2 { font-size: 18px; color: var(--accent); }
  .hint { font-size: 13px; color: var(--muted); line-height: 1.4; }

  .bet-input-row { display: flex; flex-direction: column; gap: 8px; }
  .bet-input-row label { font-size: 14px; color: var(--muted); }

  #bet-amount {
    background: var(--panel-2);
    border: 1px solid #2f3a48;
    color: var(--accent);
    font-size: 20px;
    font-weight: 700;
    padding: 10px 14px;
    border-radius: 10px;
    outline: none;
    width: 100%;
    transition: border-color .2s;
  }
  #bet-amount:focus { border-color: var(--accent); }

  .quick-bets { display: flex; flex-wrap: wrap; gap: 6px; }
  .quick-bets button {
    flex: 1 1 auto;
    background: var(--panel-2);
    border: 1px solid #2f3a48;
    color: var(--text);
    padding: 6px 8px;
    border-radius: 8px;
    font-size: 12px;
    cursor: pointer;
    transition: .15s;
  }
  .quick-bets button:hover { border-color: var(--accent); color: var(--accent); }

  .predict-row { display: flex; flex-direction: column; gap: 10px; margin-top: 6px; }

  .predict-btn {
    display: flex;
    align-items: center;
    justify-content: space-between;
    padding: 14px 16px;
    border-radius: 12px;
    border: 2px solid transparent;
    font-size: 15px;
    font-weight: 700;
    cursor: pointer;
    transition: .2s;
    color: #fff;
  }
  .predict-btn .mult { font-size: 13px; opacity: .8; }

  .predict-btn.survive {
    background: linear-gradient(135deg, #1e8449, #27ae60);
    box-shadow: 0 4px 0 #145a32;
  }
  .predict-btn.die {
    background: linear-gradient(135deg, #922b21, #c0392b);
    box-shadow: 0 4px 0 #641e16;
  }
  .predict-btn:hover:not(:disabled) { transform: translateY(-2px); filter: brightness(1.1); }
  .predict-btn:active:not(:disabled) { transform: translateY(2px); box-shadow: none; }
  .predict-btn:disabled { opacity: .4; cursor: not-allowed; }

  /* ===== Игровое поле ===== */
  .game-area { position: relative; }

  canvas {
    width: 100%;
    height: auto;
    display: block;
    border-radius: 14px;
    border: 1px solid #2a3441;
    background: #22262b;
    box-shadow: 0 8px 30px rgba(0,0,0,.5);
  }

  /* ===== Оверлей ===== */
  .overlay {
    position: absolute;
    inset: 0;
    background: rgba(6, 9, 14, .82);
    backdrop-filter: blur(4px);
    display: flex;
    align-items: center;
    justify-content: center;
    border-radius: 14px;
    z-index: 10;
    animation: fadeIn .25s ease;
  }
  .overlay.hidden { display: none; }

  @keyframes fadeIn { from { opacity: 0; } to { opacity: 1; } }

  .overlay-content {
    text-align: center;
    padding: 30px 40px;
    background: var(--panel);
    border: 1px solid #2f3a48;
    border-radius: 18px;
    max-width: 420px;
    box-shadow: 0 10px 40px rgba(0,0,0,.6);
    animation: pop .3s cubic-bezier(.34,1.56,.64,1);
  }
  @keyframes pop { from { transform: scale(.85); opacity: 0; } to { transform: scale(1); opacity: 1; } }

  .overlay-title { font-size: 30px; font-weight: 900; margin-bottom: 12px; }
  .overlay-title.win { color: var(--green); text-shadow: 0 0 20px rgba(46,204,113,.5); }
  .overlay-title.lose { color: var(--red); text-shadow: 0 0 20px rgba(231,76,60,.5); }
  .overlay-text { font-size: 16px; color: var(--muted); line-height: 1.5; margin-bottom: 22px; }

  .overlay-btn {
    background: linear-gradient(135deg, var(--accent-2), var(--accent));
    color: #1a1a1a;
    border: none;
    padding: 12px 34px;
    border-radius: 10px;
    font-size: 16px;
    font-weight: 800;
    cursor: pointer;
    transition: .2s;
  }
  .overlay-btn:hover { transform: scale(1.05); filter: brightness(1.1); }

  /* ===== Сайдбар ===== */
  .sidebar h3 { font-size: 15px; color: var(--accent); margin-bottom: 4px; }
  .stat-row {
    display: flex;
    justify-content: space-between;
    font-size: 13px;
    color: var(--muted);
    padding: 5px 0;
    border-bottom: 1px dashed #2a3441;
  }
  .stat-row:last-child { border-bottom: none; }
  .stat-row span:last-child { color: var(--text); font-weight: 700; }

  .history-title { margin-top: 6px; }
  .history-list { display: flex; flex-direction: column; gap: 4px; max-height: 160px; overflow-y: auto; }
  .history-item {
    font-size: 12px;
    padding: 5px 8px;
    border-radius: 6px;
    display: flex;
    justify-content: space-between;
    background: var(--panel-2);
  }
  .history-item.win { border-left: 3px solid var(--green); }
  .history-item.lose { border-left: 3px solid var(--red); }

  .history-list::-webkit-scrollbar { width: 6px; }
  .history-list::-webkit-scrollbar-thumb { background: #2f3a48; border-radius: 3px; }

  /* ===== Индикатор множителя ===== */
  .live-info {
    display: flex;
    justify-content: center;
    gap: 24px;
    background: var(--panel);
    border: 1px solid #2a3441;
    border-radius: 14px;
    padding: 10px 20px;
    font-size: 13px;
    color: var(--muted);
  }
  .live-info b { color: var(--accent); }
</style>
</head>
<body>

<div id="app">
  <!-- Шапка -->
  <header class="topbar">
    <div class="logo">🦆 DUCK CASINO</div>
    <div class="balance-box">
      <span class="balance-label">Баланс:</span>
      <span id="balance" class="balance-value">1000</span>
      <span class="coin">🪙</span>
    </div>
    <button id="reset-btn" class="reset-btn" title="Сбросить баланс">⟳</button>
  </header>

  <!-- Живая инфа -->
  <div class="live-info">
    <span>Текущая ставка: <b id="live-bet">100</b></span>
    <span>Последний результат: <b id="live-result">—</b></span>
  </div>

  <div class="layout">
    <!-- Панель ставки -->
    <section class="bet-panel">
      <h2>Сделай ставку</h2>
      <p class="hint">Утка пытается перейти дорогу. Выбери исход: дойдёт до конца или её собьют. Угадал — забираешь множитель!</p>

      <div class="bet-input-row">
        <label for="bet-amount">Ставка (монеты):</label>
        <input type="number" id="bet-amount" value="100" min="10" step="10">
        <div class="quick-bets">
          <button data-bet="50">50</button>
          <button data-bet="100">100</button>
          <button data-bet="250">250</button>
          <button data-bet="500">500</button>
          <button data-bet="all">ALL</button>
        </div>
      </div>

      <div class="predict-row">
        <button id="predict-survive" class="predict-btn survive">
          🦆 Дойдёт <span class="mult">x2.0</span>
        </button>
        <button id="predict-die" class="predict-btn die">
          💀 Собьют <span class="mult">x1.8</span>
        </button>
      </div>
    </section>

    <!-- Игровое поле -->
    <section class="game-area">
      <canvas id="game-canvas" width="820" height="480"></canvas>
      <div id="overlay" class="overlay hidden">
        <div class="overlay-content">
          <div id="overlay-title" class="overlay-title"></div>
          <div id="overlay-text" class="overlay-text"></div>
          <button id="overlay-btn" class="overlay-btn">Продолжить</button>
        </div>
      </div>
    </section>

    <!-- Сайдбар -->
    <aside class="sidebar">
      <h3>Статистика</h3>
      <div class="stat-row"><span>Побед</span><span id="stat-wins">0</span></div>
      <div class="stat-row"><span>Поражений</span><span id="stat-losses">0</span></div>
      <div class="stat-row"><span>Макс. баланс</span><span id="stat-max">1000</span></div>
      <div class="stat-row"><span>Всего ставок</span><span id="stat-bets">0</span></div>
      <h3 class="history-title">История</h3>
      <div id="history" class="history-list"></div>
    </aside>
  </div>
</div>

<script>
/* =========================================================
   🦆 DUCK CASINO — утиное казино на виртуальные монеты
   Никаких реальных денег. Только развлечение.
   ========================================================= */

/* ---------- Константы игры ---------- */
const CANVAS_W = 820;
const CANVAS_H = 480;
const ROAD_TOP = 140;      // верх дороги
const ROAD_BOTTOM = 340;   // низ дороги
const DUCK_SIZE = 34;
const START_BALANCE = 1000;

const MULT_SURVIVE = 2.0;  // множитель при угадывании "дойдёт"
const MULT_DIE = 1.8;      // множитель при угадывании "собьют"

// Вероятности (честная математика казино с небольшим перевесом)
// Вероятность что утка реально дойдёт
const P_SURVIVE = 0.45;

/* ---------- DOM ---------- */
const canvas = document.getElementById('game-canvas');
const ctx = canvas.getContext('2d');

const balanceEl = document.getElementById('balance');
const betInput = document.getElementById('bet-amount');
const btnSurvive = document.getElementById('predict-survive');
const btnDie = document.getElementById('predict-die');
const overlay = document.getElementById('overlay');
const overlayTitle = document.getElementById('overlay-title');
const overlayText = document.getElementById('overlay-text');
const overlayBtn = document.getElementById('overlay-btn');
const resetBtn = document.getElementById('reset-btn');

const liveBet = document.getElementById('live-bet');
const liveResult = document.getElementById('live-result');

const statWins = document.getElementById('stat-wins');
const statLosses = document.getElementById('stat-losses');
const statMax = document.getElementById('stat-max');
const statBets = document.getElementById('stat-bets');
const historyEl = document.getElementById('history');

/* ---------- Состояние ---------- */
const state = {
  balance: START_BALANCE,
  maxBalance: START_BALANCE,
  wins: 0,
  losses: 0,
  bets: 0,
  isPlaying: false,
  duck: null,
  cars: [],
  prediction: null,   // 'survive' | 'die'
  betAmount: 0,
  animFrame: null,
  lastTimestamp: 0,
  roadOffset: 0,
  finished: false,
};

/* ---------- Утилиты ---------- */
function rand(min, max) {
  return Math.random() * (max - min) + min;
}
function randInt(min, max) {
  return Math.floor(rand(min, max + 1));
}
function clamp(v, a, b) {
  return Math.max(a, Math.min(b, v));
}
function money(n) {
  return n.toLocaleString('ru-RU');
}

/* ---------- Баланс / UI ---------- */
function updateBalanceUI(flash) {
  balanceEl.textContent = money(state.balance);
  if (flash === 'win') {
    balanceEl.classList.add('flash-win');
    setTimeout(() => balanceEl.classList.remove('flash-win'), 600);
  } else if (flash === 'lose') {
    balanceEl.classList.add('flash-lose');
    setTimeout(() => balanceEl.classList.remove('flash-lose'), 600);
  }
  if (state.balance > state.maxBalance) {
    state.maxBalance = state.balance;
  }
  statWins.textContent = state.wins;
  statLosses.textContent = state.losses;
  statMax.textContent = money(state.maxBalance);
  statBets.textContent = state.bets;
}

function addHistory(win, amount, note) {
  const item = document.createElement('div');
  item.className = 'history-item ' + (win ? 'win' : 'lose');
  item.innerHTML = `<span>${note}</span><span>${win ? '+' : '−'}${money(amount)}</span>`;
  historyEl.prepend(item);
  while (historyEl.children.length > 30) {
    historyEl.removeChild(historyEl.lastChild);
  }
}

/* ---------- Утка ---------- */
function createDuck() {
  return {
    x: CANVAS_W / 2,
    y: ROAD_BOTTOM + 30,   // стартует внизу
    targetY: ROAD_TOP - 30, // цель — вверху
    wobble: 0,
    alive: true,
    progress: 0,
    speed: rand(0.9, 1.15),
    hit: false,
  };
}

/* ---------- Машины ---------- */
function createCar(laneY, direction) {
  return {
    x: direction > 0 ? -120 : CANVAS_W + 120,
    y: laneY,
    w: randInt(70, 110),
    h: 34,
    speed: direction * rand(2.2, 4.2),
    color: pickCarColor(),
    direction,
  };
}

function pickCarColor() {
  const colors = ['#e74c3c', '#3498db', '#9b59b6', '#f39c12', '#1abc9c', '#e67e22', '#ecf0f1', '#34495e'];
  return colors[randInt(0, colors.length - 1)];
}

/* ---------- Отрисовка сцены ---------- */
function drawBackground() {
  // Трава сверху
  ctx.fillStyle = '#2d4a2b';
  ctx.fillRect(0, 0, CANVAS_W, ROAD_TOP);
  // Трава снизу
  ctx.fillStyle = '#2d4a2b';
  ctx.fillRect(0, ROAD_BOTTOM, CANVAS_W, CANVAS_H - ROAD_BOTTOM);

  // Текстура травы (простые травинки)
  ctx.strokeStyle = 'rgba(120, 200, 100, 0.18)';
  ctx.lineWidth = 2;
  for (let i = 0; i < 60; i++) {
    const x = (i * 97) % CANVAS_W;
    const y = (i * 53) % ROAD_TOP;
    ctx.beginPath();
    ctx.moveTo(x, y + 10);
    ctx.lineTo(x + 3, y);
    ctx.stroke();

    const y2 = ROAD_BOTTOM + ((i * 47) % (CANVAS_H - ROAD_BOTTOM));
    ctx.beginPath();
    ctx.moveTo(x, y2 + 10);
    ctx.lineTo(x - 3, y2);
    ctx.stroke();
  }

  // Асфальт
  const roadGrad = ctx.createLinearGradient(0, ROAD_TOP, 0, ROAD_BOTTOM);
  roadGrad.addColorStop(0, '#33383f');
  roadGrad.addColorStop(0.5, '#3d434b');
  roadGrad.addColorStop(1, '#33383f');
  ctx.fillStyle = roadGrad;
  ctx.fillRect(0, ROAD_TOP, CANVAS_W, ROAD_BOTTOM - ROAD_TOP);

  // Бордюры
  ctx.fillStyle = '#d8d8d8';
  ctx.fillRect(0, ROAD_TOP - 6, CANVAS_W, 6);
  ctx.fillRect(0, ROAD_BOTTOM, CANVAS_W, 6);

  // Разметка (пунктир между полосами)
  const laneCount = 3;
  const laneHeight = (ROAD_BOTTOM - ROAD_TOP) / laneCount;
  ctx.strokeStyle = 'rgba(245, 197, 66, 0.85)';
  ctx.lineWidth = 4;
  ctx.setLineDash([28, 22]);
  ctx.lineDashOffset = -state.roadOffset;
  for (let i = 1; i < laneCount; i++) {
    const y = ROAD_TOP + laneHeight * i;
    ctx.beginPath();
    ctx.moveTo(0, y);
    ctx.lineTo(CANVAS_W, y);
    ctx.stroke();
  }
  ctx.setLineDash([]);

  // Полосы движения
  const lanes = getLanes();
  ctx.strokeStyle = 'rgba(255,255,255,0.05)';
  ctx.lineWidth = 1;
  lanes.forEach(y => {
    ctx.beginPath();
    ctx.moveTo(0, y);
    ctx.lineTo(CANVAS_W, y);
    ctx.stroke();
  });
}

function getLanes() {
  const laneCount = 3;
  const laneHeight = (ROAD_BOTTOM - ROAD_TOP) / laneCount;
  const lanes = [];
  for (let i = 0; i < laneCount; i++) {
    lanes.push(ROAD_TOP + laneHeight * i + laneHeight / 2);
  }
  return lanes;
}

function drawCar(car) {
  const x = car.x;
  const y = car.y;
  const w = car.w;
  const h = car.h;

  // Тень
  ctx.fillStyle = 'rgba(0,0,0,0.35)';
  ctx.beginPath();
  ctx.ellipse(x + w / 2, y + h / 2 + 6, w / 2, h / 3, 0, 0, Math.PI * 2);
  ctx.fill();

  // Кузов
  ctx.fillStyle = car.color;
  roundRect(ctx, x, y - h / 2, w, h, 8);
  ctx.fill();

  // Крыша
  ctx.fillStyle = 'rgba(0,0,0,0.22)';
  roundRect(ctx, x + w * 0.22, y - h / 2 + 5, w * 0.5, h * 0.45, 5);
  ctx.fill();

  // Лобовое стекло (светлее)
  ctx.fillStyle = 'rgba(160, 220, 255, 0.55)';
  roundRect(ctx, x + w * 0.25, y - h / 2 + 7, w * 0.2, h * 0.3, 3);
  ctx.fill();
  roundRect(ctx, x + w * 0.58, y - h / 2 + 7, w * 0.2, h * 0.3, 3);
  ctx.fill();

  // Колёса
  ctx.fillStyle = '#111';
  const wheelR = 8;
  ctx.beginPath(); ctx.arc(x + w * 0.2, y + h / 2, wheelR, 0, Math.PI * 2); ctx.fill();
  ctx.beginPath(); ctx.arc(x + w * 0.8, y + h / 2, wheelR, 0, Math.PI * 2); ctx.fill();

  // Фары
  ctx.fillStyle = '#fff7c2';
  if (car.direction > 0) {
    ctx.beginPath(); ctx.arc(x + w - 4, y - h / 2 + 8, 4, 0, Math.PI * 2); ctx.fill();
    ctx.beginPath(); ctx.arc(x + w - 4, y + h / 2 - 8, 4, 0, Math.PI * 2); ctx.fill();
  } else {
    ctx.beginPath(); ctx.arc(x + 4, y - h / 2 + 8, 4, 0, Math.PI * 2); ctx.fill();
    ctx.beginPath(); ctx.arc(x + 4, y + h / 2 - 8, 4, 0, Math.PI * 2); ctx.fill();
  }
}

function roundRect(ctx, x, y, w, h, r) {
  ctx.beginPath();
  ctx.moveTo(x + r, y);
  ctx.arcTo(x + w, y, x + w, y + h, r);
  ctx.arcTo(x + w, y + h, x, y + h, r);
  ctx.arcTo(x, y + h, x, y, r);
  ctx.arcTo(x, y, x + w, y, r);
  ctx.closePath();
}

function drawDuck(duck) {
  const x = duck.x;
  const y = duck.y;
  const s = DUCK_SIZE;

  // Тень
  ctx.fillStyle = 'rgba(0,0,0,0.3)';
  ctx.beginPath();
  ctx.ellipse(x, y + s * 0.7, s * 0.6, s * 0.22, 0, 0, Math.PI * 2);
  ctx.fill();

  const wob = Math.sin(duck.wobble) * 0.12;

  ctx.save();
  ctx.translate(x, y);
  ctx.rotate(wob);

  // Тело
  ctx.fillStyle = duck.hit ? '#9a9a9a' : '#f7d84b';
  ctx.beginPath();
  ctx.ellipse(0, 0, s * 0.55, s * 0.45, 0, 0, Math.PI * 2);
  ctx.fill();

  // Голова
  ctx.beginPath();
  ctx.arc(s * 0.3, -s * 0.3, s * 0.3, 0, Math.PI * 2);
  ctx.fillStyle = duck.hit ? '#8a8a8a' : '#ffe066';
  ctx.fill();

  // Клюв
  ctx.fillStyle = duck.hit ? '#7a5a20' : '#ff9500';
  ctx.beginPath();
  ctx.ellipse(s * 0.62, -s * 0.25, s * 0.18, s * 0.09, 0, 0, Math.PI * 2);
  ctx.fill();

  // Глаз
  ctx.fillStyle = '#1a1a1a';
  ctx.beginPath();
  ctx.arc(s * 0.38, -s * 0.38, s * 0.06, 0, Math.PI * 2);
  ctx.fill();

  // Крыло
  ctx.fillStyle = duck.hit ? '#7a7a7a' : '#e0c33e';
  ctx.beginPath();
  ctx.ellipse(-s * 0.05, s * 0.05, s * 0.25, s * 0.16, -0.3, 0, Math.PI * 2);
  ctx.fill();

  // Лапки
  ctx.strokeStyle = duck.hit ? '#7a5a20' : '#ff9500';
  ctx.lineWidth = 3;
  ctx.beginPath();
  ctx.moveTo(-s * 0.1, s * 0.35);
  ctx.lineTo(-s * 0.15, s * 0.6);
  ctx.stroke();
  ctx.beginPath();
  ctx.moveTo(s * 0.1, s * 0.35);
  ctx.lineTo(s * 0.05, s * 0.6);
  ctx.stroke();

  ctx.restore();
}

function drawFinishFlag() {
  // Финишная линия сверху (куда должна дойти утка)
  ctx.fillStyle = 'rgba(46, 204, 113, 0.9)';
  const y = ROAD_TOP - 20;
  for (let x = 0; x < CANVAS_W; x += 20) {
    ctx.fillRect(x, y, 10, 12);
    ctx.fillRect(x + 10, y + 6, 10, 6);
  }
}

/* ---------- Основной цикл отрисовки ---------- */
function render() {
  ctx.clearRect(0, 0, CANVAS_W, CANVAS_H);
  drawBackground();
  drawFinishFlag();

  // Машины
  state.cars.forEach(drawCar);

  // Утка
  if (state.duck) drawDuck(state.duck);

  // Подсказка "старт/финиш"
  ctx.fillStyle = 'rgba(255,255,255,0.5)';
  ctx.font = 'bold 13px Segoe UI';
  ctx.textAlign = 'left';
  ctx.fillText('ФИНИШ →', 14, ROAD_TOP - 28);
  ctx.fillText('СТАРТ', 14, ROAD_BOTTOM + 28);
}

/* ---------- Игровой цикл ---------- */
function gameLoop(timestamp) {
  if (!state.isPlaying && !state.finished) {
    render();
    return;
  }

  const dt = state.lastTimestamp ? Math.min((timestamp - state.lastTimestamp) / 16.67, 3) : 1;
  state.lastTimestamp = timestamp;

  // Движение разметки
  state.roadOffset = (state.roadOffset + 2 * dt) % 100;

  // Машины
  state.cars.forEach(car => {
    car.x += car.speed * dt;
  });
  // Удаляем уехавшие
  state.cars = state.cars.filter(car => {
    if (car.direction > 0) return car.x < CANVAS_W + 200;
    return car.x > -200;
  });

  // Спавн машин (если идёт игра)
  if (state.isPlaying) {
    spawnCars();
  }

  // Утка
  if (state.duck && state.duck.alive && !state.finished) {
    updateDuck(dt);
  }

  render();

  if (state.isPlaying) {
    state.animFrame = requestAnimationFrame(gameLoop);
  } else {
    // финальная отрисовка после остановки
    render();
    state.animFrame = null;
  }
}

function spawnCars() {
  const lanes = getLanes();
  lanes.forEach((laneY, idx) => {
    // Вероятность спавна в каждой полосе каждый кадр
    if (Math.random() < 0.012) {
      const direction = idx % 2 === 0 ? 1 : -1;
      // ограничение: не слишком много машин в полосе рядом
      const tooClose = state.cars.some(c =>
        Math.abs(c.y - laneY) < 1 &&
        (direction > 0 ? c.x < 120 : c.x > CANVAS_W - 120)
      );
      if (!tooClose) {
        state.cars.push(createCar(laneY, direction));
      }
    }
  });
}

function updateDuck(dt) {
  const duck = state.duck;
  duck.wobble += 0.18 * dt;

  // Движение вверх к финишу
  duck.y -= duck.speed * dt;

  // Небольшое горизонтальное покачивание при переходе
  duck.x += Math.sin(duck.wobble * 0.5) * 0.6 * dt;
  duck.x = clamp(duck.x, 60, CANVAS_W - 60);

  // Прогресс
  const totalDist = (ROAD_BOTTOM + 30) - (ROAD_TOP - 30);
  const passed = (ROAD_BOTTOM + 30) - duck.y;
  duck.progress = clamp(passed / totalDist, 0, 1);

  // Проверка столкновения с машинами
  for (const car of state.cars) {
    if (collidesWithCar(duck, car)) {
      duck.alive = false;
      duck.hit = true;
      finishGame('die');
      return;
    }
  }

  // Дошла до финиша
  if (duck.y <= ROAD_TOP - 10) {
    duck.y = ROAD_TOP - 10;
    finishGame('survive');
    return;
  }
}

function collidesWithCar(duck, car) {
  // Хитбокс утки (чуть меньше визуального)
  const dW = DUCK_SIZE * 0.8;
  const dH = DUCK_SIZE * 0.8;
  const dx = duck.x - dW / 2;
  const dy = duck.y - dH / 2;

  const cx = car.x;
  const cy = car.y - car.h / 2;
  const cw = car.w;
  const ch = car.h;

  return dx < cx + cw && dx + dW > cx && dy < cy + ch && dy + dH > cy;
}

/* ---------

/* ---------- Старт раунда ---------- */
function startRound(prediction) {
  if (state.isPlaying) return;

  // Парсим ставку
  let bet = parseInt(betInput.value, 10);
  if (isNaN(bet) || bet < 10) {
    bet = 10;
    betInput.value = 10;
  }
  if (bet > state.balance) {
    bet = state.balance;
    betInput.value = bet;
  }
  if (bet <= 0 || state.balance <= 0) {
    showOverlay('lose', 'Недостаточно монет', 'Пополни баланс кнопкой ⟳ вверху или сыграй заново.', 'Ок');
    return;
  }

  // Списываем ставку
  state.balance -= bet;
  state.betAmount = bet;
  state.prediction = prediction;
  state.bets++;
  liveBet.textContent = money(bet);
  updateBalanceUI();

  // Сброс состояния сцены
  state.cars = [];
  state.roadOffset = 0;
  state.finished = false;
  state.duck = createDuck();
  state.isPlaying = true;
  state.lastTimestamp = 0;

  // Блокируем кнопки
  setButtonsDisabled(true);

  // Подсказка
  liveResult.textContent = '...';

  // Запускаем цикл
  if (state.animFrame) cancelAnimationFrame(state.animFrame);
  state.animFrame = requestAnimationFrame(gameLoop);
}

/* ---------- Завершение раунда ---------- */
function finishGame(actualOutcome) {
  // actualOutcome: 'survive' | 'die' — что реально случилось с уткой
  state.isPlaying = false;
  state.finished = true;

  const predicted = state.prediction;
  const bet = state.betAmount;
  const won = predicted === actualOutcome;

  let payout = 0;
  let netChange = 0;

  if (won) {
    const mult = actualOutcome === 'survive' ? MULT_SURVIVE : MULT_DIE;
    payout = Math.round(bet * mult);
    netChange = payout - bet; // ставка уже списана
    state.balance += payout;
    state.wins++;
    updateBalanceUI('win');
    addHistory(true, netChange, actualOutcome === 'survive' ? 'Дошла' : 'Сбили (угадал)');
  } else {
    netChange = -bet;
    state.losses++;
    updateBalanceUI('lose');
    addHistory(false, bet, actualOutcome === 'survive' ? 'Дошла (не угадал)' : 'Сбили');
  }

  liveResult.textContent = won ? `Победа +${money(netChange)}` : `Проигрыш −${money(bet)}`;

  // Показываем оверлей с результатом
  setTimeout(() => {
    if (won) {
      const mult = actualOutcome === 'survive' ? MULT_SURVIVE : MULT_DIE;
      const title = actualOutcome === 'survive' ? '🦆 УТКА ДОШЛА!' : '💥 ТАК И ЗНАЛИ!';
      const text = `Ты угадал! Выплата: ${money(payout)} (x${mult})<br>Чистая прибыль: <b>+${money(netChange)}</b>`;
      showOverlay('win', title, text, 'Играть ещё');
    } else {
      const title = actualOutcome === 'survive' ? '🦆 УТКА ВЫЖИЛА' : '💀 УТКУ СБИЛИ';
      const text = `Ставка не сыграла. Потеряно: <b>−${money(bet)}</b><br>Не повезло — попробуй снова!`;
      showOverlay('lose', title, text, 'Играть ещё');
    }

    // Плавно останавливаем цикл
    if (state.animFrame) {
      cancelAnimationFrame(state.animFrame);
      state.animFrame = null;
    }
    render();
  }, 900);

  setButtonsDisabled(false);
}

/* ---------- Оверлей ---------- */
function showOverlay(type, title, text, btnLabel) {
  overlayTitle.textContent = title;
  overlayTitle.className = 'overlay-title ' + type;
  overlayText.innerHTML = text;
  overlayBtn.textContent = btnLabel || 'Продолжить';
  overlay.classList.remove('hidden');
}

function hideOverlay() {
  overlay.classList.add('hidden');
}

/* ---------- Кнопки ---------- */
function setButtonsDisabled(disabled) {
  btnSurvive.disabled = disabled;
  btnDie.disabled = disabled;
  resetBtn.disabled = disabled;
}

/* ---------- Обработчики ---------- */
btnSurvive.addEventListener('click', () => startRound('survive'));
btnDie.addEventListener('click', () => startRound('die'));

overlayBtn.addEventListener('click', () => {
  hideOverlay();
  // Разрешаем новую ставку
  state.finished = false;
  // Очищаем сцену от прошлой игры
  state.cars = [];
  state.duck = createDuck();
  state.duck.y = ROAD_BOTTOM + 30; // стоит на старте
  state.duck.alive = true;
  render();
});

// Быстрые ставки
document.querySelectorAll('.quick-bets button').forEach(btn => {
  btn.addEventListener('click', () => {
    const val = btn.dataset.bet;
    if (val === 'all') {
      betInput.value = state.balance;
    } else {
      betInput.value = Math.min(parseInt(val, 10), state.balance);
    }
    liveBet.textContent = money(parseInt(betInput.value, 10) || 0);
  });
});

// Обновление подсказки ставки
betInput.addEventListener('input', () => {
  let v = parseInt(betInput.value, 10);
  if (isNaN(v) || v < 0) v = 0;
  liveBet.textContent = money(v);
});

// Сброс прогресса
resetBtn.addEventListener('click', () => {
  if (state.isPlaying) return;
  if (!confirm('Сбросить весь прогресс и начать с 1000 монет?')) return;

  state.balance = START_BALANCE;
  state.maxBalance = START_BALANCE;
  state.wins = 0;
  state.losses = 0;
  state.bets = 0;
  state.cars = [];
  state.duck = createDuck();
  state.duck.alive = true;
  state.finished = false;
  historyEl.innerHTML = '';
  liveResult.textContent = '—';
  betInput.value = 100;
  liveBet.textContent = '100';
  hideOverlay();
  updateBalanceUI();
  render();
});

/* ---------- Клавиатура (хоткеи) ---------- */
document.addEventListener('keydown', (e) => {
  if (state.isPlaying) return;

  if (e.key === '1') {
    startRound('survive');
  } else if (e.key === '2') {
    startRound('die');
  } else if (e.key === 'Escape') {
    hideOverlay();
  } else if (e.key === 'Enter') {
    if (!overlay.classList.contains('hidden')) {
      overlayBtn.click();
    }
  }
});

/* ---------- Инициализация ---------- */
function init() {
  updateBalanceUI();
  state.duck = createDuck();
  state.duck.y = ROAD_BOTTOM + 30; // стоит на старте
  state.duck.alive = true;
  liveBet.textContent = '100';
  render();

  // Показываем вступительный оверлей при первом запуске
  setTimeout(() => {
    showOverlay(
      'win',
      '🦆 Добро пожаловать!',
      'Это <b>виртуальное казино</b> — только фейковые монеты, никаких реальных денег.<br><br>' +
      'Правила простые: утка пытается перейти дорогу. Ты ставишь на исход:<br>' +
      '<b>🦆 Дойдёт</b> (x2.0) или <b>💀 Собьют</b> (x1.8).<br><br>' +
      'Угадал — получаешь выплату. Не угадал — теряешь ставку.<br>' +
      'Удачи, охотник за утками!',
      'Погнали!'
    );
  }, 400);
}

init();
</script>
</body>
</html>
