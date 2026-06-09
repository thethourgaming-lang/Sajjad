<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no">
<title>🎯 Archery Master</title>
<style>
  @import url('https://fonts.googleapis.com/css2?family=Cinzel:wght@400;700;900&family=Rajdhani:wght@300;500;700&display=swap');

  * { margin: 0; padding: 0; box-sizing: border-box; touch-action: none; }

  :root {
    --gold: #f0c040;
    --amber: #e8871a;
    --dark: #0a0a0f;
    --deep: #12121a;
    --panel: #1a1a28;
    --glow: #ff6b35;
    --wood: #8B5E3C;
    --string: #d4af7a;
  }

  body {
    background: var(--dark);
    font-family: 'Rajdhani', sans-serif;
    overflow: hidden;
    height: 100vh;
    width: 100vw;
    cursor: crosshair;
  }

  /* STARS BACKGROUND */
  #stars {
    position: fixed; inset: 0; pointer-events: none; z-index: 0;
  }

  /* INTRO SCREEN */
  #intro {
    position: fixed; inset: 0; z-index: 100;
    background: radial-gradient(ellipse at center, #1a0a00 0%, #0a0a0f 70%);
    display: flex; flex-direction: column; align-items: center; justify-content: center;
    gap: 30px;
  }

  .intro-glow {
    position: absolute; inset: 0;
    background: radial-gradient(ellipse at 50% 40%, rgba(240,192,64,0.08) 0%, transparent 60%);
    pointer-events: none;
  }

  .intro-title {
    font-family: 'Cinzel', serif;
    font-size: clamp(42px, 10vw, 90px);
    font-weight: 900;
    color: var(--gold);
    text-shadow: 0 0 30px rgba(240,192,64,0.6), 0 0 60px rgba(240,192,64,0.3), 0 0 100px rgba(240,192,64,0.1);
    letter-spacing: 6px;
    animation: titlePulse 2s ease-in-out infinite;
    text-align: center;
  }

  @keyframes titlePulse {
    0%,100% { text-shadow: 0 0 30px rgba(240,192,64,0.6), 0 0 60px rgba(240,192,64,0.3); }
    50% { text-shadow: 0 0 50px rgba(240,192,64,0.9), 0 0 100px rgba(240,192,64,0.5), 0 0 150px rgba(240,192,64,0.2); }
  }

  .intro-subtitle {
    font-family: 'Rajdhani', sans-serif;
    font-size: clamp(14px, 3vw, 22px);
    color: rgba(240,192,64,0.6);
    letter-spacing: 8px;
    text-transform: uppercase;
  }

  .intro-target {
    width: 140px; height: 140px;
    position: relative;
    animation: targetSpin 8s linear infinite;
  }

  @keyframes targetSpin {
    from { transform: rotate(0deg); }
    to { transform: rotate(360deg); }
  }

  .start-btn {
    font-family: 'Cinzel', serif;
    font-size: 20px;
    font-weight: 700;
    letter-spacing: 4px;
    padding: 16px 50px;
    background: transparent;
    border: 2px solid var(--gold);
    color: var(--gold);
    cursor: pointer;
    text-transform: uppercase;
    position: relative;
    overflow: hidden;
    transition: all 0.3s;
    animation: btnGlow 2s ease-in-out infinite 1s;
  }

  @keyframes btnGlow {
    0%,100% { box-shadow: 0 0 10px rgba(240,192,64,0.3); }
    50% { box-shadow: 0 0 25px rgba(240,192,64,0.7), 0 0 50px rgba(240,192,64,0.3); }
  }

  .start-btn::before {
    content: '';
    position: absolute; inset: 0;
    background: var(--gold);
    transform: translateX(-100%);
    transition: transform 0.3s;
    z-index: -1;
  }

  .start-btn:hover::before, .start-btn:active::before { transform: translateX(0); }
  .start-btn:hover, .start-btn:active { color: var(--dark); }

  .instructions {
    color: rgba(255,255,255,0.4);
    font-size: 14px;
    text-align: center;
    letter-spacing: 1px;
    line-height: 1.8;
  }

  /* GAME SCREEN */
  #game {
    position: fixed; inset: 0; z-index: 10;
    display: none;
  }

  #gameCanvas {
    position: absolute; inset: 0;
    width: 100%; height: 100%;
  }

  /* HUD */
  #hud {
    position: absolute; top: 0; left: 0; right: 0;
    padding: 16px 24px;
    display: flex; justify-content: space-between; align-items: center;
    z-index: 20;
    background: linear-gradient(to bottom, rgba(0,0,0,0.7) 0%, transparent 100%);
  }

  .hud-score {
    font-family: 'Cinzel', serif;
    font-size: clamp(22px, 5vw, 36px);
    color: var(--gold);
    text-shadow: 0 0 15px rgba(240,192,64,0.5);
    font-weight: 700;
  }

  .hud-label {
    font-size: 11px;
    letter-spacing: 3px;
    color: rgba(240,192,64,0.5);
    text-transform: uppercase;
    display: block;
    text-align: center;
  }

  .hud-arrows {
    display: flex; gap: 8px; align-items: center;
  }

  .arrow-icon {
    width: 28px; height: 6px;
    background: linear-gradient(to right, var(--wood), #c8a06a);
    border-radius: 3px;
    position: relative;
    transition: opacity 0.3s;
  }

  .arrow-icon.used {
    opacity: 0.15;
    background: #333;
  }

  .arrow-icon::after {
    content: '▶';
    position: absolute;
    right: -8px;
    top: 50%;
    transform: translateY(-50%);
    font-size: 9px;
    color: var(--gold);
  }

  .arrow-icon.used::after { color: #333; }

  .hud-level {
    font-family: 'Cinzel', serif;
    font-size: clamp(16px, 3vw, 22px);
    color: rgba(255,255,255,0.7);
    text-align: right;
  }

  /* POWER METER */
  #powerMeter {
    position: absolute;
    bottom: 80px;
    left: 50%;
    transform: translateX(-50%);
    width: min(300px, 80vw);
    z-index: 20;
    display: none;
    flex-direction: column;
    align-items: center;
    gap: 6px;
  }

  .power-label {
    font-size: 12px;
    letter-spacing: 3px;
    color: rgba(255,255,255,0.5);
    text-transform: uppercase;
  }

  .power-track {
    width: 100%;
    height: 8px;
    background: rgba(255,255,255,0.1);
    border-radius: 4px;
    overflow: hidden;
    border: 1px solid rgba(255,255,255,0.1);
  }

  .power-fill {
    height: 100%;
    width: 0%;
    border-radius: 4px;
    transition: width 0.05s, background 0.1s;
    background: linear-gradient(to right, #4ade80, #f0c040, #ef4444);
  }

  /* SCORE POPUP */
  .score-popup {
    position: absolute;
    font-family: 'Cinzel', serif;
    font-size: 28px;
    font-weight: 900;
    pointer-events: none;
    z-index: 30;
    animation: popFloat 1.2s ease-out forwards;
    text-shadow: 0 2px 10px rgba(0,0,0,0.8);
  }

  @keyframes popFloat {
    0% { transform: translateY(0) scale(0.5); opacity: 1; }
    30% { transform: translateY(-20px) scale(1.2); opacity: 1; }
    100% { transform: translateY(-80px) scale(1); opacity: 0; }
  }

  /* WIND INDICATOR */
  #windIndicator {
    position: absolute;
    top: 80px;
    left: 50%;
    transform: translateX(-50%);
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 4px;
    z-index: 20;
  }

  .wind-label {
    font-size: 11px;
    letter-spacing: 3px;
    color: rgba(255,255,255,0.4);
    text-transform: uppercase;
  }

  .wind-bar {
    display: flex;
    align-items: center;
    gap: 8px;
  }

  .wind-arrow {
    font-size: 18px;
    color: rgba(100,200,255,0.7);
    transition: transform 0.5s;
  }

  .wind-strength {
    font-size: 13px;
    color: rgba(100,200,255,0.7);
    letter-spacing: 1px;
  }

  /* GAME OVER */
  #gameOver {
    position: fixed; inset: 0; z-index: 50;
    background: rgba(0,0,0,0.85);
    display: none;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    gap: 20px;
    backdrop-filter: blur(10px);
  }

  .go-title {
    font-family: 'Cinzel', serif;
    font-size: clamp(36px, 8vw, 72px);
    color: var(--gold);
    font-weight: 900;
    text-shadow: 0 0 30px rgba(240,192,64,0.5);
    letter-spacing: 4px;
  }

  .go-score {
    font-family: 'Rajdhani', sans-serif;
    font-size: clamp(18px, 4vw, 28px);
    color: rgba(255,255,255,0.7);
    letter-spacing: 2px;
  }

  .go-score span {
    color: var(--gold);
    font-family: 'Cinzel', serif;
    font-size: 1.4em;
    font-weight: 700;
  }

  .replay-btn {
    font-family: 'Cinzel', serif;
    font-size: 18px;
    font-weight: 700;
    letter-spacing: 3px;
    padding: 14px 44px;
    background: var(--gold);
    border: none;
    color: var(--dark);
    cursor: pointer;
    text-transform: uppercase;
    transition: all 0.2s;
  }

  .replay-btn:hover, .replay-btn:active {
    background: var(--amber);
    transform: scale(1.05);
  }

  /* LEVEL UP */
  #levelUp {
    position: fixed; inset: 0; z-index: 45;
    display: none;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    pointer-events: none;
  }

  .levelup-text {
    font-family: 'Cinzel', serif;
    font-size: clamp(40px, 9vw, 80px);
    font-weight: 900;
    color: var(--gold);
    text-shadow: 0 0 40px rgba(240,192,64,0.8);
    animation: levelPop 2s ease-out forwards;
    letter-spacing: 4px;
  }

  @keyframes levelPop {
    0% { transform: scale(0) rotate(-10deg); opacity: 0; }
    20% { transform: scale(1.2) rotate(2deg); opacity: 1; }
    40% { transform: scale(1) rotate(0deg); opacity: 1; }
    70% { transform: scale(1) rotate(0deg); opacity: 1; }
    100% { transform: scale(0.8) translateY(-30px); opacity: 0; }
  }

  /* BULLSEYE FLASH */
  #bullseyeFlash {
    position: fixed; inset: 0; z-index: 40;
    pointer-events: none;
    display: none;
  }

  .flash-ring {
    position: absolute;
    top: 50%; left: 50%;
    transform: translate(-50%, -50%);
    border-radius: 50%;
    border: 3px solid var(--gold);
    animation: ringExpand 0.8s ease-out forwards;
  }

  @keyframes ringExpand {
    0% { width: 0; height: 0; opacity: 1; }
    100% { width: 300px; height: 300px; opacity: 0; }
  }
</style>
</head>
<body>

<canvas id="stars"></canvas>

<!-- INTRO -->
<div id="intro">
  <div class="intro-glow"></div>
  <div class="intro-subtitle">◆ Master the Ancient Art ◆</div>
  <div class="intro-title">🎯 ARCHERY<br>MASTER</div>
  <svg class="intro-target" viewBox="0 0 140 140">
    <circle cx="70" cy="70" r="68" fill="#1a0a00" stroke="rgba(240,192,64,0.2)" stroke-width="1"/>
    <circle cx="70" cy="70" r="60" fill="#2a1500" stroke="rgba(240,192,64,0.3)" stroke-width="1.5"/>
    <circle cx="70" cy="70" r="48" fill="#331a00" stroke="rgba(240,192,64,0.4)" stroke-width="1.5"/>
    <circle cx="70" cy="70" r="36" fill="#3d2200" stroke="rgba(240,192,64,0.5)" stroke-width="1.5"/>
    <circle cx="70" cy="70" r="24" fill="#b8860b" stroke="rgba(240,192,64,0.7)" stroke-width="1.5"/>
    <circle cx="70" cy="70" r="12" fill="#f0c040" stroke="rgba(255,255,255,0.3)" stroke-width="1"/>
    <circle cx="70" cy="70" r="4" fill="white"/>
    <line x1="70" y1="2" x2="70" y2="138" stroke="rgba(240,192,64,0.15)" stroke-width="0.5"/>
    <line x1="2" y1="70" x2="138" y2="70" stroke="rgba(240,192,64,0.15)" stroke-width="0.5"/>
  </svg>
  <button class="start-btn" id="startBtn">SHOOT NOW</button>
  <div class="instructions">
    Touch &amp; hold to aim • Release to shoot<br>
    Hit the bullseye for maximum points
  </div>
</div>

<!-- GAME -->
<div id="game">
  <canvas id="gameCanvas"></canvas>

  <div id="hud">
    <div>
      <span class="hud-label">Score</span>
      <div class="hud-score" id="scoreDisplay">0</div>
    </div>
    <div id="arrowsHud" class="hud-arrows"></div>
    <div>
      <span class="hud-label">Level</span>
      <div class="hud-level" id="levelDisplay">1</div>
    </div>
  </div>

  <div id="windIndicator">
    <div class="wind-label">Wind</div>
    <div class="wind-bar">
      <span class="wind-arrow" id="windArrow">→</span>
      <span class="wind-strength" id="windStrength">0.0 m/s</span>
    </div>
  </div>

  <div id="powerMeter">
    <div class="power-label">Draw Power</div>
    <div class="power-track">
      <div class="power-fill" id="powerFill"></div>
    </div>
  </div>
</div>

<!-- GAME OVER -->
<div id="gameOver">
  <div class="go-title">GAME OVER</div>
  <div class="go-score">Final Score: <span id="finalScore">0</span></div>
  <div class="go-score">Best: <span id="bestScore">0</span></div>
  <button class="replay-btn" id="replayBtn">PLAY AGAIN</button>
</div>

<!-- LEVEL UP -->
<div id="levelUp">
  <div class="levelup-text" id="levelUpText">LEVEL UP!</div>
</div>

<!-- BULLSEYE FLASH -->
<div id="bullseyeFlash" id="bullseyeFlash"></div>

<script>
// ===== STARS =====
(function() {
  const c = document.getElementById('stars');
  const ctx = c.getContext('2d');
  c.width = window.innerWidth; c.height = window.innerHeight;
  const stars = Array.from({length: 200}, () => ({
    x: Math.random()*c.width, y: Math.random()*c.height,
    r: Math.random()*1.5+0.3,
    a: Math.random(), da: (Math.random()-0.5)*0.005
  }));
  function draw() {
    ctx.clearRect(0,0,c.width,c.height);
    stars.forEach(s => {
      s.a = Math.max(0.1, Math.min(1, s.a+s.da));
      if(s.a<=0.1||s.a>=1) s.da*=-1;
      ctx.beginPath();
      ctx.arc(s.x,s.y,s.r,0,Math.PI*2);
      ctx.fillStyle = `rgba(255,255,240,${s.a*0.7})`;
      ctx.fill();
    });
    requestAnimationFrame(draw);
  }
  draw();
  window.addEventListener('resize', () => { c.width=window.innerWidth; c.height=window.innerHeight; });
})();

// ===== GAME =====
const canvas = document.getElementById('gameCanvas');
const ctx = canvas.getContext('2d');
let W, H;

function resize() {
  W = canvas.width = window.innerWidth;
  H = canvas.height = window.innerHeight;
}
resize();
window.addEventListener('resize', resize);

// Game state
let score = 0, bestScore = 0, level = 1, arrows = 7, maxArrows = 7;
let isAiming = false, power = 0, powerDir = 1;
let aimStart = {x:0,y:0}, aimCurrent = {x:0,y:0};
let projectiles = [], particles = [], arrowStuck = [];
let wind = {dx: 0, speed: 0};
let targetPos = {x:0, y:0}, targetSize = 80;
let targetMoveDir = 1, targetSpeed = 0;
let animFrame;
let gameActive = false;
let combo = 0;

// Target rings
const rings = [
  {r: 1.0, color: '#2a1500', points: 1, label: '1'},
  {r: 0.8, color: '#331a00', points: 2, label: '2'},
  {r: 0.6, color: '#3d2200', points: 4, label: '4'},
  {r: 0.4, color: '#8B4513', points: 7, label: '7'},
  {r: 0.22, color: '#b8860b', points: 9, label: '9'},
  {r: 0.12, color: '#f0c040', points: 10, label: '10'},
];

function initLevel() {
  targetSize = Math.max(40, 90 - (level-1)*5);
  targetSpeed = (level-1) * 0.4;
  targetPos.x = W/2;
  targetPos.y = H * 0.28;
  wind.speed = (level-1) * 0.3 * (Math.random()-0.5) * 2;
  wind.dx = wind.speed;
  updateWindUI();
}

function updateWindUI() {
  const wa = document.getElementById('windArrow');
  const ws = document.getElementById('windStrength');
  ws.textContent = Math.abs(wind.speed).toFixed(1) + ' m/s';
  wa.textContent = wind.speed > 0.1 ? '→' : wind.speed < -0.1 ? '←' : '•';
  wa.style.color = Math.abs(wind.speed) > 1.5 ? 'rgba(255,100,100,0.8)' : 'rgba(100,200,255,0.7)';
}

function updateArrowsHud() {
  const hud = document.getElementById('arrowsHud');
  hud.innerHTML = '';
  for(let i=0;i<maxArrows;i++) {
    const d = document.createElement('div');
    d.className = 'arrow-icon' + (i >= arrows ? ' used' : '');
    hud.appendChild(d);
  }
}

// Draw background
function drawBackground() {
  // Sky gradient
  const sky = ctx.createLinearGradient(0,0,0,H);
  sky.addColorStop(0,'#050510');
  sky.addColorStop(0.5,'#0a0818');
  sky.addColorStop(1,'#1a100a');
  ctx.fillStyle = sky;
  ctx.fillRect(0,0,W,H);

  // Ground
  const gY = H*0.75;
  ctx.fillStyle = '#1a0f05';
  ctx.fillRect(0, gY, W, H-gY);

  // Ground gradient
  const gr = ctx.createLinearGradient(0,gY,0,H);
  gr.addColorStop(0,'rgba(139,94,60,0.4)');
  gr.addColorStop(1,'rgba(60,30,10,0.2)');
  ctx.fillStyle = gr;
  ctx.fillRect(0,gY,W,H-gY);

  // Distant mountains
  ctx.fillStyle = 'rgba(20,10,30,0.7)';
  drawMountain(0, gY, W*0.3, gY*0.55);
  drawMountain(W*0.2, gY, W*0.35, gY*0.5);
  drawMountain(W*0.5, gY, W*0.28, gY*0.6);
  drawMountain(W*0.65, gY, W*0.4, gY*0.48);

  // Fog
  const fog = ctx.createLinearGradient(0, gY-60, 0, gY+20);
  fog.addColorStop(0,'transparent');
  fog.addColorStop(1,'rgba(30,15,5,0.5)');
  ctx.fillStyle = fog;
  ctx.fillRect(0,gY-60,W,80);
}

function drawMountain(x,base,w,peak) {
  ctx.beginPath();
  ctx.moveTo(x,base);
  ctx.lineTo(x+w*0.5,peak);
  ctx.lineTo(x+w,base);
  ctx.closePath();
  ctx.fill();
}

// Draw target
function drawTarget() {
  const {x,y} = targetPos;
  const R = targetSize;

  ctx.save();
  // Shadow glow
  ctx.shadowColor = 'rgba(240,192,64,0.2)';
  ctx.shadowBlur = 20;

  rings.forEach(ring => {
    ctx.beginPath();
    ctx.arc(x, y, R*ring.r, 0, Math.PI*2);
    ctx.fillStyle = ring.color;
    ctx.fill();
  });

  // Ring borders
  rings.forEach(ring => {
    ctx.beginPath();
    ctx.arc(x, y, R*ring.r, 0, Math.PI*2);
    ctx.strokeStyle = 'rgba(240,192,64,0.25)';
    ctx.lineWidth = 0.8;
    ctx.stroke();
  });

  // Crosshair
  ctx.strokeStyle = 'rgba(240,192,64,0.2)';
  ctx.lineWidth = 0.5;
  ctx.beginPath(); ctx.moveTo(x-R, y); ctx.lineTo(x+R, y); ctx.stroke();
  ctx.beginPath(); ctx.moveTo(x, y-R); ctx.lineTo(x, y+R); ctx.stroke();

  ctx.restore();
}

// Draw archer
function drawArcher(drawBow) {
  const ax = W*0.1, ay = H*0.72;
  const scale = Math.min(W,H)/600;

  ctx.save();
  ctx.translate(ax, ay);

  // Legs
  ctx.strokeStyle = '#5a3a20';
  ctx.lineWidth = 5*scale;
  ctx.lineCap = 'round';
  ctx.beginPath(); ctx.moveTo(0,-40*scale); ctx.lineTo(-10*scale,0); ctx.stroke();
  ctx.beginPath(); ctx.moveTo(0,-40*scale); ctx.lineTo(10*scale,5*scale); ctx.stroke();

  // Body
  ctx.beginPath(); ctx.moveTo(0,0); ctx.lineTo(0,-40*scale); ctx.stroke();

  // Head
  ctx.beginPath();
  ctx.arc(0, -60*scale, 10*scale, 0, Math.PI*2);
  ctx.fillStyle = '#c8a060';
  ctx.fill();

  // Bow arm (horizontal)
  ctx.beginPath(); ctx.moveTo(0,-42*scale); ctx.lineTo(30*scale,-30*scale); ctx.stroke();

  if(drawBow && isAiming) {
    // Draw bow with pull
    const pullAmount = power * 15 * scale;
    ctx.save();
    ctx.translate(30*scale, -30*scale);

    // Bow curve
    ctx.strokeStyle = var_wood();
    ctx.lineWidth = 3*scale;
    ctx.beginPath();
    ctx.moveTo(0, -20*scale);
    ctx.quadraticCurveTo(-8*scale - pullAmount, 0, 0, 20*scale);
    ctx.stroke();

    // String
    ctx.strokeStyle = '#d4af7a';
    ctx.lineWidth = 1.5*scale;
    ctx.beginPath();
    ctx.moveTo(0, -20*scale);
    ctx.lineTo(-pullAmount, 0);
    ctx.lineTo(0, 20*scale);
    ctx.stroke();

    // Arrow on bow
    ctx.strokeStyle = '#8B5E3C';
    ctx.lineWidth = 2.5*scale;
    ctx.beginPath();
    ctx.moveTo(-pullAmount, 0);
    ctx.lineTo(25*scale, 0);
    ctx.stroke();
    // Arrowhead
    ctx.fillStyle = '#d0d0d0';
    ctx.beginPath();
    ctx.moveTo(25*scale, 0);
    ctx.lineTo(20*scale, -3*scale);
    ctx.lineTo(20*scale, 3*scale);
    ctx.closePath();
    ctx.fill();
    ctx.restore();
  } else if(!isAiming) {
    // Resting bow
    ctx.save();
    ctx.translate(30*scale, -30*scale);
    ctx.strokeStyle = var_wood();
    ctx.lineWidth = 3*scale;
    ctx.beginPath();
    ctx.moveTo(0, -20*scale);
    ctx.quadraticCurveTo(-8*scale, 0, 0, 20*scale);
    ctx.stroke();
    ctx.strokeStyle = '#d4af7a';
    ctx.lineWidth = 1.5*scale;
    ctx.beginPath();
    ctx.moveTo(0, -20*scale); ctx.lineTo(0, 20*scale);
    ctx.stroke();
    ctx.restore();
  }

  // Aim line when aiming
  if(isAiming) {
    const tx = targetPos.x - ax;
    const ty = targetPos.y - ay;
    const ang = Math.atan2(ty - (-30*scale), tx - 30*scale);
    ctx.save();
    ctx.strokeStyle = 'rgba(255,200,50,0.15)';
    ctx.lineWidth = 1;
    ctx.setLineDash([5,8]);
    ctx.beginPath();
    ctx.moveTo(30*scale, -30*scale);
    ctx.lineTo(tx*1.5, ty);
    ctx.stroke();
    ctx.setLineDash([]);
    ctx.restore();
  }

  ctx.restore();
}

function var_wood() { return '#6b3a1f'; }

// Projectile
function shootArrow() {
  if(arrows <= 0 || !gameActive) return;
  arrows--;
  updateArrowsHud();

  const ax = W*0.1 + 30*(Math.min(W,H)/600);
  const ay = H*0.72 - 30*(Math.min(W,H)/600);
  const tx = targetPos.x;
  const ty = targetPos.y;
  const dist = Math.hypot(tx-ax, ty-ay);
  const speed = 10 + power * 12;
  const vx = (tx-ax)/dist * speed;
  const vy = (ty-ay)/dist * speed - power * 3;

  projectiles.push({x:ax, y:ay, vx, vy, trail:[], power, wind: wind.dx*0.02});
}

function updateProjectiles() {
  projectiles.forEach((p,i) => {
    p.trail.push({x:p.x, y:p.y});
    if(p.trail.length > 12) p.trail.shift();
    p.x += p.vx + p.w
