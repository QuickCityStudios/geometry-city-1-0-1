
<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>GEOMETRY CITY - V1.1.4 (RESET UI UPDATE)</title>
    <style>
        body, html { margin: 0; padding: 0; width: 100%; height: 100%; overflow: hidden; background: #050510; font-family: 'Arial Black', 'Segoe UI', sans-serif; }
        canvas { display: block; }
        
        /* UI GLOBAL */
        #coin-display { position: absolute; top: 20px; left: 20px; color: #f1c40f; font-size: 26px; z-index: 2000; font-weight: bold; text-shadow: 2px 2px #000; }
        #mission-btn { 
            position: absolute; top: 20px; right: 85px; width: 55px; height: 55px; 
            background: #ffffff; border-radius: 12px; cursor: pointer; z-index: 2000;
            display: flex; align-items: center; justify-content: center; font-size: 28px;
            box-shadow: 0 4px 10px rgba(0,0,0,0.3); transition: 0.3s cubic-bezier(0.175, 0.885, 0.32, 1.275); border: none;
        }
        #mission-btn:hover { transform: scale(1.1); }
        #mission-btn.complete { background: #f1c40f; animation: pulse 1.5s infinite; }
        
        #tips-container { 
            position: absolute; bottom: 20px; right: 20px; color: white; font-size: 13px; 
            width: 240px; background: rgba(0,0,0,0.6); padding: 10px; border-radius: 8px; 
            border-right: 4px solid #00ffcc; z-index: 2000; pointer-events: none;
            font-family: 'Segoe UI', sans-serif; backdrop-filter: blur(5px);
        }
        
        #toast { 
            position: absolute; top: 100px; left: 50%; transform: translateX(-50%); 
            padding: 15px 30px; border-radius: 50px; color: white; font-weight: bold;
            z-index: 4000; display: none; animation: slideDown 0.5s; font-family: 'Segoe UI', sans-serif;
            box-shadow: 0 5px 15px rgba(0,0,0,0.5); text-align: center;
        }

        #live-missions {
            position: absolute; bottom: 20px; left: 20px; z-index: 10;
            display: flex; flex-direction: column; gap: 8px; font-family: 'Segoe UI', sans-serif;
            opacity: 0; transform: translateX(-50px); transition: 0.5s all ease;
        }
        #live-missions.show { opacity: 1; transform: translateX(0); }
        .lm-item {
            background: rgba(0, 0, 0, 0.7); backdrop-filter: blur(8px);
            border-left: 4px solid #f1c40f; padding: 10px 15px; border-radius: 8px;
            color: #fff; width: 190px; box-shadow: 0 4px 6px rgba(0,0,0,0.3);
            display: flex; align-items: center; gap: 10px;
        }
        .lm-bar-bg { width: 100%; height: 6px; background: rgba(255,255,255,0.15); border-radius: 3px; overflow: hidden; margin-top: 4px; }
        .lm-bar-fill { height: 100%; background: #f1c40f; width: 0%; transition: width 0.3s; }

        #ui-top { position: absolute; top: 15px; width: 100%; display: flex; flex-direction: column; align-items: center; pointer-events: none; z-index: 10; opacity: 0; transition: 0.5s opacity; }
        #percentage { color: #fff; font-size: 28px; text-shadow: 2px 2px #000; }
        #progress-container { width: 50%; height: 12px; background: rgba(255,255,255,0.2); border: 2px solid #fff; border-radius: 6px; overflow: hidden; }
        #progress-bar { width: 0%; height: 100%; background: #00ffcc; box-shadow: 0 0 15px #00ffcc; }

        #pause-trigger { position: absolute; top: 20px; right: 20px; width: 55px; height: 55px; background: rgba(255,255,255,0.2); border: 3px solid #fff; color: white; border-radius: 12px; cursor: pointer; z-index: 2000; font-size: 24px; display: none; align-items: center; justify-content: center; transition: 0.2s; }

        .overlay { position: absolute; top: 0; left: 0; width: 100%; height: 100%; background: rgba(0,0,0,0.85); display: flex; flex-direction: column; justify-content: center; align-items: center; z-index: 1500; color: white; text-align: center; }
        .shop-box { background: #1a1a2e; padding: 25px; border-radius: 20px; border: 3px solid #fff; width: 85%; max-width: 500px; font-family: 'Segoe UI', sans-serif; box-shadow: 0 0 30px rgba(0,0,0,0.5); }
        .shop-grid { display: grid; grid-template-columns: repeat(4, 1fr); gap: 10px; margin: 15px 0; max-height: 250px; overflow-y: auto; padding: 10px; background: rgba(0,0,0,0.3); border-radius: 10px; }
        .item { background: #2c3e50; padding: 10px; border-radius: 10px; cursor: pointer; border: 2px solid transparent; display: flex; flex-direction: column; align-items: center; transition: 0.2s; }
        .item.selected { border-color: #00ffcc; background: #34495e; box-shadow: 0 0 10px #00ffcc; }
        .item span { margin-top: 5px; color: #f1c40f; font-size: 11px; font-weight: bold; }
        
        .icon-btn { width: 80px; height: 80px; background: #34495e; border: 4px solid #fff; border-radius: 20px; cursor: pointer; display: flex; align-items: center; justify-content: center; font-size: 35px; margin: 10px; transition: 0.1s; border-bottom: 6px solid #2c3e50; }
        .icon-btn:active { transform: translateY(4px); border-bottom-width: 2px; }

        .btn-reset { margin-top: 15px; background: #e74c3c; color: white; border: none; padding: 10px; border-radius: 8px; cursor: pointer; font-weight: bold; width: 100%; font-size: 14px; transition: 0.3s; }
        .btn-reset:hover { background: #c0392b; box-shadow: 0 0 10px rgba(231, 76, 60, 0.5); }

        .confirm-btns { display: flex; gap: 15px; justify-content: center; margin-top: 20px; }
        .confirm-btn { padding: 12px 30px; border-radius: 10px; border: none; cursor: pointer; font-weight: bold; font-size: 16px; transition: 0.2s; }
        .btn-yes { background: #2ecc71; color: white; }
        .btn-no { background: #e74c3c; color: white; }
        .confirm-btn:hover { transform: scale(1.05); }

        @keyframes pulse { 0% { box-shadow: 0 0 0 0 rgba(241, 196, 15, 0.7); } 70% { box-shadow: 0 0 0 15px rgba(241, 196, 15, 0); } 100% { box-shadow: 0 0 0 0 rgba(241, 196, 15, 0); } }
        @keyframes slideDown { from { top: -50px; opacity: 0; } to { top: 100px; opacity: 1; } }
    </style>
/* Bloqueia qualquer movimento de tela el�stica no Android */
body {
    touch-action: none;
    -webkit-tap-highlight-color: transparent;
    user-select: none;
}
</head>
<body>

<div id="toast"></div>
<div id="coin-display">💰 <span id="coins">0</span></div>
<div id="mission-btn" onclick="uiClick(); openMissions()">📒</div>
<div id="pause-trigger" onclick="uiClick(); pauseGame()">⏸</div>
<div id="tips-container">DICA: Carregando...</div>
<div id="live-missions"></div>

<div id="ui-top">
    <div id="percentage">0%</div>
    <div id="progress-container"><div id="progress-bar"></div></div>
</div>

<div id="menu" class="overlay">
    <h1 style="color:#00ffcc; font-size: 45px; text-shadow: 0 0 15px #00ffcc; margin-bottom: 5px;">GEOMETRY CITY</h1>
    <p style="margin-top:0; color: #aaa;">V1.1.4 | RESET UI</p>
    <div style="display:flex;">
        <div onclick="uiClick(); openConfig()" class="icon-btn">⚙️</div>
        <div onclick="uiClick(); startGame()" class="icon-btn" style="background:#2ecc71">▶️</div>
        <div onclick="uiClick(); openShop()" class="icon-btn">🛒</div>
    </div>
</div>

<div id="missionMenu" class="overlay" style="display:none; z-index: 3000;">
    <div class="shop-box">
        <h2 id="mission-tier-label" style="color:#f1c40f; margin-top:0;">MISSÕES</h2>
        <div id="mission-content" style="text-align: left;"></div>
        <button onclick="uiClick(); closeOverlays()" style="margin-top:15px; padding:12px 30px; background:#e74c3c; color:white; border:none; border-radius:8px; cursor:pointer; font-weight:bold;">FECHAR</button>
    </div>
</div>

<div id="shopMenu" class="overlay" style="display: none; z-index: 3000;">
    <div class="shop-box">
        <h3 style="color:#00ffcc; margin-top:0;">LOJA DE SKINS</h3>
        <div id="color-grid" class="shop-grid"></div>
        <button onclick="uiClick(); closeOverlays()" style="padding:10px 25px; background:#e74c3c; color:white; border:none; border-radius:8px; cursor:pointer; font-weight:bold;">VOLTAR</button>
    </div>
</div>

<div id="pauseMenu" class="overlay" style="display: none; z-index: 3000;">
    <h1 style="color:#f1c40f;">PAUSADO</h1>
    <div onclick="uiClick(); resumeGame()" class="icon-btn" style="background:#2ecc71">▶️</div>
    <div onclick="uiClick(); location.reload()" class="icon-btn" style="background:#e74c3c;">🏠</div>
</div>

<div id="configMenu" class="overlay" style="display: none; z-index: 3000;">
    <div class="shop-box">
        <h2 style="margin-top:0;">AJUSTES</h2>
        Música: <input type="range" id="musicVol" min="0" max="0.1" step="0.01"><br><br>
        SFX: <input type="range" id="sfxVol" min="0" max="0.2" step="0.01"><br>
        <button onclick="uiClick(); saveSettings()" style="margin-top:20px; padding:10px 20px; background:#2ecc71; color:white; border:none; border-radius:5px; cursor:pointer; font-weight:bold;">SALVAR</button>
        <button onclick="uiClick(); openResetConfirm()" class="btn-reset">⚠️ REDEFINIR TUDO</button>
    </div>
</div>

<div id="resetConfirmMenu" class="overlay" style="display: none; z-index: 4000; background: rgba(0,0,0,0.95);">
    <div class="shop-box" style="border-color: #e74c3c;">
        <h2 style="color:#e74c3c; margin-top:0;">TEM CERTEZA?</h2>
        <p>Isso apagará suas moedas, skins e progresso de missões permanentemente.</p>
        <div class="confirm-btns">
            <button onclick="uiClick(); executeFullReset()" class="confirm-btn btn-yes">SIM</button>
            <button onclick="uiClick(); closeResetConfirm()" class="confirm-btn btn-no">NÃO</button>
        </div>
    </div>
</div>

<div id="gameOverScreen" class="overlay" style="display: none; z-index: 3000;">
    <h1 style="color:#e74c3c; font-size: 50px;">BATEU!</h1>
    <div onclick="uiClick(); startGame()" class="icon-btn" style="background:#2ecc71">▶️</div>
    <div onclick="uiClick(); location.reload()" class="icon-btn">🏠</div>
</div>

<div id="winScreen" class="overlay" style="display: none; z-index: 3000;">
    <h1 style="color:#00ffcc; font-size: 55px; text-shadow: 0 0 20px #00ffcc;">VITÓRIA!</h1>
    <div onclick="uiClick(); location.reload()" class="icon-btn" style="background:#2ecc71">🏠</div>
</div>

<canvas id="game"></canvas>

<script>
// --- CONSTANTES & DATA ---
const canvas = document.getElementById('game');
const ctx = canvas.getContext('2d');
const audioCtx = new (window.AudioContext || window.webkitAudioContext)();
const GRAVITY = 1.4, JUMP_FORCE = -18, SPEED = 17, LEVEL_LENGTH = 60000;

const skins = [
    {id: 0, name:"Cyan", color:"#00ffcc", price:0},
    {id: 1, name:"Sky", color:"#3498db", price:150},
    {id: 2, name:"Ruby", color:"#e74c3c", price:300},
    {id: 3, name:"Gold", color:"#f1c40f", price:600},
    {id: 4, name:"Purple", color:"#9b59b6", price:900},
    {id: 5, name:"Rosa", color:"#ff69b4", price:1200},
    {id: 6, name:"Verde", color:"#2ecc71", price:1500}
];

const missionPresets = {
    1: [{id:'m1', text:'Pular 20 vezes', goal:20, reward:50, type:'jumps'}, {id:'m2', text:'Pegar 25 moedas', goal:25, reward:100, type:'coins'}, {id:'m3', text:'Chegar a 50%', goal:50, reward:150, type:'progress'}],
    2: [{id:'m4', text:'Pegar 60 moedas', goal:60, reward:200, type:'coins'}, {id:'m5', text:'Pular 100 vezes', goal:100, reward:250, type:'jumps'}, {id:'m6', text:'Vencer 1 fase', goal:1, reward:300, type:'wins'}],
    3: [{id:'m7', text:'150 Moedas', goal:150, reward:400, type:'coins'}, {id:'m8', text:'300 Pulos', goal:300, reward:500, type:'jumps'}, {id:'m9', text:'Vencer 3 fases', goal:3, reward:600, type:'wins'}]
};

// --- SAVE SYSTEM ---
let totalCoins, musicVol, sfxVol, missionTier, selectedSkinId, ownedSkins, missionsData;

function initDefaults() {
    totalCoins = 0;
    musicVol = 0.04;
    sfxVol = 0.08;
    missionTier = 1;
    selectedSkinId = 0;
    ownedSkins = [0];
    missionsData = missionPresets[1].map(m => ({...m, current:0, done:false, claimed:false}));
}

function loadGame() {
    const saved = localStorage.getItem('gc_v114_save');
    if(saved) {
        const data = JSON.parse(saved);
        totalCoins = data.coins;
        musicVol = data.mVol;
        sfxVol = data.sVol;
        missionTier = data.tier;
        selectedSkinId = data.skin;
        ownedSkins = data.owned;
        missionsData = data.missions;
    } else {
        initDefaults();
    }
    updateUI();
}

function saveGame() {
    const saveObj = {
        coins: totalCoins,
        mVol: musicVol,
        sVol: sfxVol,
        tier: missionTier,
        skin: selectedSkinId,
        owned: ownedSkins,
        missions: missionsData
    };
    localStorage.setItem('gc_v114_save', JSON.stringify(saveObj));
    updateUI();
}

// Funções de Reset Customizado
function openResetConfirm() {
    document.getElementById('resetConfirmMenu').style.display = 'flex';
}

function closeResetConfirm() {
    document.getElementById('resetConfirmMenu').style.display = 'none';
}

function executeFullReset() {
    localStorage.removeItem('gc_v114_save');
    location.reload();
}

function updateUI() {
    document.getElementById('coins').innerText = totalCoins;
    const hasUnclaimed = missionsData.some(m => m.done && !m.claimed);
    document.getElementById('mission-btn').classList.toggle('complete', hasUnclaimed);
}

// --- SOUNDS ---
function playSound(f, d, v, type='square') {
    if(audioCtx.state === 'suspended') audioCtx.resume();
    const o = audioCtx.createOscillator(); const g = audioCtx.createGain();
    o.type = type; o.frequency.setValueAtTime(f, audioCtx.currentTime);
    g.gain.setValueAtTime(v, audioCtx.currentTime);
    g.gain.exponentialRampToValueAtTime(0.00001, audioCtx.currentTime + d);
    o.connect(g); g.connect(audioCtx.destination);
    o.start(); o.stop(audioCtx.currentTime + d); return o;
}
const uiClick = () => playSound(800, 0.1, sfxVol, 'sine');
const soundJump = () => { const o = playSound(200, 0.2, sfxVol, 'triangle'); o.frequency.exponentialRampToValueAtTime(600, audioCtx.currentTime + 0.1); };
const soundLose = () => playSound(100, 0.5, sfxVol, 'sawtooth');
const soundWin = () => [523, 659, 783].forEach((f, i) => setTimeout(() => playSound(f, 0.4, sfxVol, 'sine'), i * 150));

let mInt;
function updateMusic(mode) {
    clearInterval(mInt); let step = 0;
    mInt = setInterval(() => {
        if(!isPaused && (mode === 'menu' || gameRunning)) {
            if(mode === 'menu') playSound([261, 329][step % 2], 0.4, musicVol, 'triangle');
            else { playSound(60, 0.2, musicVol * 2, 'square'); playSound([110, 130, 150][step % 3], 0.1, musicVol, 'sawtooth'); }
            step++;
        }
    }, 180);
}

// --- GAME STATE ---
let gameRunning = false, isPaused = false, isDying = false, isWinning = false, score = 0;
let player = { x: 150, y: 0, vy: 0, rotation: 0, onGround: false };
let obstacles = [], coins = [], city = [], particles = [], trail = [];
let isPressing = false;

function resize() { canvas.width = window.innerWidth; canvas.height = window.innerHeight; }
window.addEventListener('resize', resize); resize();

function startGame() {
    document.querySelectorAll('.overlay').forEach(el => el.style.display='none');
    document.getElementById('pause-trigger').style.display = 'flex';
    document.getElementById('ui-top').style.opacity = '1';
    document.getElementById('mission-btn').style.transform = 'scale(0)';
    
    particles = []; obstacles = []; coins = []; city = []; trail = [];
    score = 0; player.y = canvas.height - 155; player.vy = 0; player.rotation = 0;
    isDying = false; isPaused = false; isWinning = false; gameRunning = true;
    
    const lm = document.getElementById('live-missions'); lm.innerHTML = '';
    missionsData.forEach((m) => { 
        if(!m.claimed) lm.innerHTML += `<div class="lm-item" id="lm-${m.id}"><div><div style="font-size:10px">${m.text}</div><div class="lm-bar-bg"><div class="lm-bar-fill" id="fill-${m.id}"></div></div></div></div>`; 
    });
    setTimeout(() => lm.classList.add('show'), 100);

    for(let i=0; i<30; i++) city.push({x: i*350, w: 150, h: 200+Math.random()*300});
    for(let i=0; i<100; i++) { 
        let x = 1500 + i*950 + (Math.random()*200);
        obstacles.push({x: x, type: Math.random() > 0.7 ? 2 : 1}); 
        if(Math.random() > 0.4) coins.push({x: x-400, y: canvas.height-140-Math.random()*60, active: true});
    }
    updateMusic('game'); requestAnimationFrame(loop);
}

function checkMission(type, val = 1) {
    let changed = false;
    missionsData.forEach(m => {
        if(m.type === type && !m.done) {
            m.current += val;
            if(m.current >= m.goal) { m.current = m.goal; m.done = true; showToast("MISSÃO COMPLETA!", "#2ecc71"); }
            changed = true;
        }
    });
    if(changed) saveGame();
}

function update() {
    if(!gameRunning || isPaused) return;
    if(isWinning || isDying) { 
        if(isDying) particles.forEach(p => { p.x += p.vx; p.y += p.vy; p.life -= 0.02; });
        if(particles.every(p => p.life <= 0) && isDying) { gameRunning = false; document.getElementById('gameOverScreen').style.display='flex'; }
        return; 
    }

    let currentGrav = (isPressing && player.vy < 0) ? GRAVITY * 0.45 : GRAVITY;
    player.vy += currentGrav; player.y += player.vy;

    if (player.y > canvas.height - 155) { player.y = canvas.height - 155; player.vy = 0; player.onGround = true; player.rotation = Math.round(player.rotation/90)*90; } 
    else { player.onGround = false; player.rotation += 8; }
    
    score += SPEED;
    let prog = Math.min(score / LEVEL_LENGTH, 1);
    let progPercent = Math.floor(prog * 100);
    
    document.getElementById('progress-bar').style.width = (prog * 100) + "%";
    document.getElementById('percentage').innerText = progPercent + "%";
    
    missionsData.forEach(m => {
        if(m.type === 'progress' && !m.done && progPercent >= m.goal) { m.current = m.goal; m.done = true; showToast("MISSÃO COMPLETA!", "#2ecc71"); saveGame(); }
        let fillEl = document.getElementById(`fill-${m.id}`);
        if(fillEl) {
            let pVal = (m.type === 'progress') ? Math.min((progPercent / m.goal) * 100, 100) : (m.current / m.goal) * 100;
            fillEl.style.width = pVal + "%";
            if(m.done) fillEl.style.background = "#2ecc71";
        }
    });

    trail.push({x: player.x+10, y: player.y+15});
    if(trail.length > 15) trail.shift();

    obstacles.forEach(obs => { 
        obs.x -= SPEED; 
        if(player.x + 40 > obs.x && player.x < obs.x + (obs.type*60) - 10 && player.y > canvas.height - 165) { 
            isDying = true; soundLose(); 
            for(let i=0; i<15; i++) particles.push({ x: player.x+25, y: player.y+25, vx: (Math.random()-0.5)*15, vy: (Math.random()-0.5)*15, life: 1.0 });
        }
    });

    coins.forEach(c => { 
        c.x -= SPEED; 
        if(c.active && Math.abs(player.x - c.x) < 45 && Math.abs(player.y - c.y) < 55) { 
            c.active = false; totalCoins++; saveGame(); playSound(1200, 0.1, sfxVol, 'sine'); checkMission('coins');
        }
    });

    if(prog >= 1 && !isWinning) { isWinning = true; soundWin(); checkMission('wins'); document.getElementById('winScreen').style.display='flex'; }
}

function draw() {
    ctx.fillStyle = "#050510"; ctx.fillRect(0,0,canvas.width,canvas.height);
    city.forEach(b => { b.x -= SPEED*0.25; if(b.x < -200) b.x = canvas.width; ctx.fillStyle = "#0a0a25"; ctx.fillRect(b.x, canvas.height-100-b.h, b.w, b.h); });
    ctx.fillStyle = "black"; ctx.fillRect(0, canvas.height-100, canvas.width, 100);
    
    trail.forEach((t, i) => { ctx.fillStyle = `rgba(255,255,255,${i/20})`; ctx.fillRect(t.x - (15-i)*10, t.y, 40, 25); });
    
    obstacles.forEach(obs => { 
        ctx.fillStyle = "black"; ctx.strokeStyle = "white"; ctx.lineWidth = 2;
        for(let n=0; n<obs.type; n++) {
            let ox = obs.x + (n*60);
            ctx.beginPath(); ctx.moveTo(ox, canvas.height-100); ctx.lineTo(ox+30, canvas.height-160); ctx.lineTo(ox+60, canvas.height-100); ctx.closePath(); ctx.fill(); ctx.stroke();
        }
    });
    
    coins.forEach(c => { if(c.active) { ctx.fillStyle = "#f1c40f"; ctx.beginPath(); ctx.arc(c.x, c.y, 14, 0, Math.PI*2); ctx.fill(); ctx.stroke(); }});
    
    const skin = skins.find(s => s.id === selectedSkinId);
    if(!isDying) {
        ctx.save(); ctx.translate(player.x+27, player.y+27); ctx.rotate(player.rotation*Math.PI/180);
        ctx.fillStyle = skin.color; ctx.fillRect(-27,-27,54,54); ctx.strokeStyle="white"; ctx.lineWidth=3; ctx.strokeRect(-27,-27,54,54); ctx.restore();
    }
    particles.forEach(p => { if(p.life > 0) { ctx.fillStyle = skin.color; ctx.globalAlpha = p.life; ctx.fillRect(p.x, p.y, 10, 10); }});
    ctx.globalAlpha = 1;
}

// --- UI HELPERS ---
function openMissions() {
    const content = document.getElementById('mission-content'); content.innerHTML = '';
    document.getElementById('mission-tier-label').innerText = `TIER ${missionTier} DE MISSÕES`;
    missionsData.forEach(m => {
        content.innerHTML += `
            <div style="background:rgba(255,255,255,0.1); padding:10px; margin-bottom:8px; border-radius:8px; display:flex; justify-content:space-between; align-items:center;">
                <div><div style="font-weight:bold;">${m.text}</div><div style="font-size:12px;">${m.current}/${m.goal}</div></div>
                ${m.done && !m.claimed ? `<button onclick="collectMission('${m.id}')" style="background:#2ecc71; color:white; border:none; padding:8px; border-radius:5px; cursor:pointer;">COLETAR</button>` : ''}
                ${m.claimed ? '✅' : ''}
            </div>`;
    });
    document.getElementById('missionMenu').style.display = 'flex';
}

function collectMission(id) {
    let m = missionsData.find(x => x.id === id);
    if(m && m.done && !m.claimed) {
        m.claimed = true; totalCoins += m.reward; 
        playSound(1000, 0.2, sfxVol, 'sine');
        if(missionsData.every(x => x.claimed)) {
            if(missionTier < 3) {
                missionTier++; 
                missionsData = missionPresets[missionTier].map(m => ({...m, current:0, done:false, claimed:false}));
                showToast(`TIER ${missionTier} DESBLOQUEADO!`, "#00ffcc");
            }
        }
        saveGame(); openMissions();
    }
}

function renderShop() {
    const cg = document.getElementById('color-grid'); cg.innerHTML = '';
    skins.forEach(s => {
        const isOwned = ownedSkins.includes(s.id);
        const d = document.createElement('div'); d.className = `item ${selectedSkinId === s.id ? 'selected' : ''}`;
        d.innerHTML = `<div style="width:35px; height:35px; background:${s.color}; border:2px solid #fff; border-radius:5px;"></div><span>${isOwned ? 'USA' : '💰'+s.price}</span>`;
        d.onclick = () => {
            if(isOwned) { selectedSkinId = s.id; saveGame(); uiClick(); renderShop(); }
            else if(totalCoins >= s.price) {
                totalCoins -= s.price; ownedSkins.push(s.id); selectedSkinId = s.id;
                saveGame(); uiClick(); renderShop(); showToast("COMPRADO!", "#2ecc71");
            } else showToast("MOEDAS INSUFICIENTES!", "#e74c3c");
        }; cg.appendChild(d);
    });
}

function loop() { update(); draw(); if(gameRunning) requestAnimationFrame(loop); }
function handlePress() { if(!gameRunning || isPaused || isDying) return; isPressing = true; if(player.onGround) { player.vy = JUMP_FORCE; soundJump(); checkMission('jumps'); } }
function pauseGame() { isPaused = true; document.getElementById('pauseMenu').style.display='flex'; }
function resumeGame() { isPaused = false; document.getElementById('pauseMenu').style.display='none'; }
function openConfig() { document.getElementById('configMenu').style.display='flex'; document.getElementById('musicVol').value = musicVol; document.getElementById('sfxVol').value = sfxVol; }
function openShop() { document.getElementById('shopMenu').style.display='flex'; renderShop(); }
function closeOverlays() { document.querySelectorAll('.overlay').forEach(el => { if(el.id !== 'menu') el.style.display='none'; }); }
function showToast(text, color="#f1c40f") { const t = document.getElementById('toast'); t.innerText = text; t.style.background = color; t.style.display = 'block'; setTimeout(() => t.style.display = 'none', 2500); }
function saveSettings() { musicVol = parseFloat(document.getElementById('musicVol').value); sfxVol = parseFloat(document.getElementById('sfxVol').value); saveGame(); closeOverlays(); showToast("SALVO!", "#2ecc71"); }

window.addEventListener('keydown', e => { if(e.code === 'Space') handlePress(); });
window.addEventListener('keyup', e => { if(e.code === 'Space') isPressing = false; });
window.addEventListener('touchstart', e => { if(e.target.tagName !== 'BUTTON' && e.target.tagName !== 'INPUT') handlePress(); });
window.addEventListener('touchend', () => isPressing = false);

// --- INIT ---
const tips = ["Segure para pular mais alto!", "Dê toques rápidos para pulos baixos.", "O rastro indica a trajetória.", "Missões dão muitas moedas.", "Cuidado com espinhos duplos!", "Skins novas dão sorte!", "Pausar descansa a visão.", "Use espaço ou toque na tela.", "Cada Tier é mais difícil.", "A porta aparece nos 90%.", "Pratique o ritmo dos saltos!", "Ajuste o som na engrenagem.", "A cor Cyan é clássica.", "Moedas azuis estão no alto.", "Não desista, tente de novo!", "Progresso salva sozinho.", "Siga o fluxo da música!", "Vença para subir de Tier.", "Complete tudo!", "Mantenha a calma!"];
setInterval(() => { document.getElementById('tips-container').innerText = "DICA: " + tips[Math.floor(Math.random()*tips.length)]; }, 7000);

loadGame();
updateMusic('menu');
</script>
</body>
</html>
