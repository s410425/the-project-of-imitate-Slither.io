<!DOCTYPE html>
<html lang="zh-Hant">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Snake.io Arena - 離線自訂特訓版</title>
    <style>
        body { margin: 0; overflow: hidden; background: #050505; font-family: 'Segoe UI', sans-serif; user-select: none; }
        canvas { display: block; }
        
        /* UI 圖層 */
        #ui-layer { position: absolute; top: 20px; right: 20px; color: #eee; background: rgba(20,20,20,0.9); padding: 15px; border-radius: 12px; font-size: 14px; border: 1px solid #444; width: 220px; box-shadow: 0 4px 10px rgba(0,0,0,0.5); pointer-events: none; }
        #score-ui { position: absolute; bottom: 20px; left: 20px; color: #0f4; font-size: 24px; font-weight: bold; text-shadow: 0 0 10px rgba(0,255,68,0.5); pointer-events: none; display: flex; flex-direction: column; gap: 5px; }
        #best-ui { color: #ff0; font-size: 16px; text-shadow: 0 0 10px rgba(255,238,0,0.5); }
        #permanent-rank { position: absolute; top: 20px; left: 20px; color: #eee; background: rgba(20,20,20,0.9); padding: 15px; border-radius: 12px; font-size: 13px; border: 1px solid #444; width: 200px; box-shadow: 0 4px 10px rgba(0,0,0,0.5); }

        /* 選單與自訂控制室 */
        #menu { position: absolute; width: 100%; height: 100%; background: rgba(0,0,0,0.95); display: flex; flex-direction: column; align-items: center; justify-content: center; color: white; z-index: 100; overflow-y: auto; padding: 20px 0; box-sizing: border-box; }
        .input-box { margin-bottom: 15px; text-align: center; }
        #player-name-input { background: transparent; border: 2px solid #333; border-radius: 8px; padding: 8px 20px; color: #0f4; font-size: 18px; text-align: center; outline: none; transition: 0.3s; }
        #player-name-input:focus { border-color: #0f4; box-shadow: 0 0 15px rgba(0,255,68,0.3); }
        
        /* 快速預設按鈕 */
        .btn-group { display: flex; gap: 10px; margin-bottom: 20px; }
        .preset-btn { background: #222; border: 1px solid #444; color: #fff; padding: 8px 16px; border-radius: 20px; cursor: pointer; font-size: 13px; transition: 0.2s; }
        .preset-btn:hover { background: #333; border-color: #0f4; }

        /* 參數設定面板 */
        .config-title { color: #0f4; font-size: 14px; font-weight: bold; letter-spacing: 1px; margin-bottom: 10px; }
        .config-panel { background: rgba(255,255,255,0.03); border: 1px solid #333; border-radius: 12px; padding: 15px; width: 85%; max-width: 520px; display: grid; grid-template-columns: 1fr 1fr; gap: 12px 20px; box-sizing: border-box; }
        .config-group { display: flex; flex-direction: column; align-items: flex-start; }
        .config-group label { color: #aaa; font-size: 12px; margin-bottom: 5px; width: 100%; display: flex; justify-content: space-between; }
        .config-group label span { color: #0af; }
        .config-group input { background: #111; border: 1px solid #444; color: #fff; padding: 6px 10px; border-radius: 6px; width: 100%; box-sizing: border-box; outline: none; font-size: 14px; }
        .config-group input:focus { border-color: #0af; }

        #start-btn { font-size: 22px; padding: 10px 60px; cursor: pointer; background: #0f4; border: none; color: #000; border-radius: 50px; font-weight: bold; transition: 0.3s; margin-top: 25px; box-shadow: 0 0 15px rgba(0,255,68,0.2); }
        #start-btn:hover { box-shadow: 0 0 30px #0f4; transform: scale(1.05); }

        h1 { font-size: 40px; margin: 0 0 5px 0; letter-spacing: 3px; text-shadow: 0 0 20px #0f4; }
        .hint { color: #555; font-size: 12px; margin-top: 15px; }
        hr { border: 0; border-top: 1px solid #333; margin: 10px 0; }
    </style>
</head>
<body>

<div id="menu">
    <h1 style="color:#0f4">SNAKE.IO <span style="color:white">TRAINING</span></h1>
    <p style="color:#888; margin: 0 0 20px 0;">離線特訓室 · 核心參數完全自訂</p>

    <div class="input-box">
        <input type="text" id="player-name-input" placeholder="GUEST" maxlength="12">
    </div>

    <div class="btn-group">
        <button class="preset-btn" onclick="applyPreset(3000, 400, 25, 150, 4.5, 300)">🟢 經典練習</button>
        <button class="preset-btn" onclick="applyPreset(3000, 600, 30, 300, 6.0, 3000)">⚡ 外掛覺醒</button>
        <button class="preset-btn" onclick="applyPreset(5000, 1000, 65, 600, 8.0, 1500)">🔴 神風地獄</button>
    </div>

    <div class="config-title">⚙️ 核心戰場參數控制台</div>
    <div class="config-panel">
        <div class="config-group">
            <label>地圖邊界大小 (MAP) <span id="val-map">3000</span></label>
            <input type="range" id="cfg-map" min="1000" max="8000" step="500" value="3000" oninput="syncVal('map')">
        </div>
        <div class="config-group">
            <label>發光食物總數 (FOOD) <span id="val-food">400</span></label>
            <input type="range" id="cfg-food" min="50" max="1500" step="50" value="400" oninput="syncVal('food')">
        </div>
        <div class="config-group">
            <label>戰場 AI 數量 (AI) <span id="val-ai">25</span></label>
            <input type="range" id="cfg-ai" min="0" max="100" step="5" value="25" oninput="syncVal('ai')">
        </div>
        <div class="config-group">
            <label>玩家初始長度 (LEN) <span id="val-len">150</span></label>
            <input type="range" id="cfg-len" min="50" max="1000" step="50" value="150" oninput="syncVal('len')">
        </div>
        <div class="config-group">
            <label>基礎移動速度 (SPEED) <span id="val-speed">4.5</span></label>
            <input type="range" id="cfg-speed" min="2" max="12" step="0.5" value="4.5" oninput="syncVal('speed')">
        </div>
        <div class="config-group">
            <label>最大磁力吸附範圍 (MAG) <span id="val-mag">3000</span></label>
            <input type="range" id="cfg-mag" min="100" max="5000" step="100" value="3000" oninput="syncVal('mag')">
        </div>
    </div>

    <button id="start-btn" onclick="startGame()">進入特訓</button>
    <p class="hint">遊戲中按 [ESC] 可隨時安全退出並紀錄最佳成績</p>
</div>

<div id="permanent-rank">
    <div id="rank-title" style="font-weight:bold; color:#ff0; margin-bottom:10px;">🏆 個人歷史最佳</div>
    <div id="p-list"></div>
</div>

<div id="ui-layer"></div>
<div id="score-ui">
    <div id="best-ui">BEST: 0</div>
    <div id="cur-ui">SCORE: 0</div>
</div>

<canvas id="gameCanvas"></canvas>

<script>
const canvas = document.getElementById("gameCanvas");
const ctx = canvas.getContext("2d");

// 全局運行狀態與即時動態參數
let started = false;
let camera = { x: 0, y: 0, zoom: 1 };
let foods = [], ai_bots = [], toDie = [];
let mouse = { x: window.innerWidth / 2, y: window.innerHeight / 2, down: false };
let playerName = "GUEST";
let player;

// 這裡的數值會在外掛/開局時被 UI 的自訂數值完全覆蓋
let RUNTIME_CONFIG = {
    map: 3000,
    foodMax: 400,
    aiCount: 25,
    startLen: 150,
    speed: 4.5,
    magnetLimit: 3000
};

// --- UI 數值即時同步與預設組切換 ---
function syncVal(key) {
    const input = document.getElementById(`cfg-${key}`);
    const span = document.getElementById(`val-${key}`);
    if (input && span) {
        span.innerText = input.value;
    }
}

function applyPreset(map, food, ai, len, speed, mag) {
    document.getElementById('cfg-map').value = map;
    document.getElementById('cfg-food').value = food;
    document.getElementById('cfg-ai').value = ai;
    document.getElementById('cfg-len').value = len;
    document.getElementById('cfg-speed').value = speed;
    document.getElementById('cfg-mag').value = mag;
    
    // 全部重新整理顯示
    ['map', 'food', 'ai', 'len', 'speed', 'mag'].forEach(syncVal);
}

// 讀取當前滑桿設定到執行環境中
function captureConfig() {
    RUNTIME_CONFIG.map = parseInt(document.getElementById('cfg-map').value);
    RUNTIME_CONFIG.foodMax = parseInt(document.getElementById('cfg-food').value);
    RUNTIME_CONFIG.aiCount = parseInt(document.getElementById('cfg-ai').value);
    RUNTIME_CONFIG.startLen = parseInt(document.getElementById('cfg-len').value);
    RUNTIME_CONFIG.speed = parseFloat(document.getElementById('cfg-speed').value);
    RUNTIME_CONFIG.magnetLimit = parseInt(document.getElementById('cfg-mag').value);
}

// --- 排行榜儲存與本地最高分刷新 ---
function updatePermanentUI() {
    let storageKey = `snake_offline_v4_records`;
    let records = JSON.parse(localStorage.getItem(storageKey) || '[]');
    
    // 更新左上角歷史前五名面板
    const list = document.getElementById('p-list');
    if(records.length === 0) { 
        list.innerHTML = "<div style='color:#555'>尚無特訓紀錄</div>"; 
        document.getElementById("best-ui").innerText = `BEST: 0`;
        return; 
    }
    list.innerHTML = records.map((r, i) => 
        `<div style="margin:5px 0">${i+1}. <span style="color:#888">${r.name}</span> <span style="float:right;color:#ff0;font-weight:bold">${r.score}</span></div>`
    ).join('');

    // 更新右下角最高分顯示
    document.getElementById("best-ui").innerText = `BEST: ${records[0].score}`;
}

function saveRecord(score) {
    if (score < 100) return; // 分數極低不記入
    let storageKey = `snake_offline_v4_records`;
    let records = JSON.parse(localStorage.getItem(storageKey) || '[]');
    
    records.push({ 
        score: Math.floor(score), 
        name: playerName,
        date: new Date().toLocaleDateString() 
    });
    
    // 降序排序，只取前五名
    records.sort((a, b) => b.score - a.score);
    localStorage.setItem(storageKey, JSON.stringify(records.slice(0, 5)));
    updatePermanentUI();
}

// --- 核心類別（貪食蛇） ---
class Snake {
    constructor(x, y, color, isPlayer = false, startLen = 150) {
        this.body = [];
        this.length = startLen;
        this.color = color;
        this.isPlayer = isPlayer;
        this.speed = RUNTIME_CONFIG.speed;
        this.angle = Math.random() * Math.PI * 2;
        this.dead = false;
        
        this.name = isPlayer ? playerName : "AI_BOT_" + Math.floor(Math.random()*999);

        // 初始化身體節點
        for (let i = 0; i < this.length; i += 5) {
            this.body.push({ x: x - i * Math.cos(this.angle), y: y - i * Math.sin(this.angle) });
        }
    }

    getWidth() { 
        return Math.min(60, 12 + Math.sqrt(this.length) * 0.5); 
    }

    update() {
        if (this.dead) return;

        // 衝刺加速機制
        let currentSpeed = this.speed;
        if (this.isPlayer && mouse.down && this.length > 100) {
            currentSpeed *= 1.8;
            if (Math.random() > 0.6) {
                this.length -= 2; // 加速扣除體長
                if (Math.random() > 0.5) {
                    spawnFood(this.body[this.body.length - 1].x, this.body[this.body.length - 1].y, false, this.color);
                }
            }
        }

        // 頭部前進
        let head = {
            x: this.body[0].x + Math.cos(this.angle) * currentSpeed,
            y: this.body[0].y + Math.sin(this.angle) * currentSpeed
        };
        this.body.unshift(head);

        // 平衡節點與總長度
        let targetSegmentCount = Math.max(5, Math.floor(this.length / 5));
        while (this.body.length > targetSegmentCount) {
            this.body.pop();
        }
    }

    draw() {
        if (this.body.length === 0) return;
        ctx.save();
        let width = this.getWidth();

        // 畫身體
        ctx.lineCap = "round";
        ctx.lineJoin = "round";
        ctx.strokeStyle = this.color;
        ctx.lineWidth = width;
        ctx.beginPath();
        ctx.moveTo(this.body[0].x, this.body[0].y);
        for (let i = 1; i < this.body.length; i++) {
            ctx.lineTo(this.body[i].x, this.body[i].y);
        }
        ctx.stroke();

        // 畫眼睛
        let head = this.body[0];
        ctx.fillStyle = "#fff";
        let eyeOffsetAngle = 0.5;
        let eyeDist = width * 0.3;
        
        let eye1X = head.x + Math.cos(this.angle + eyeOffsetAngle) * eyeDist;
        let eye1Y = head.y + Math.sin(this.angle + eyeOffsetAngle) * eyeDist;
        let eye2X = head.x + Math.cos(this.angle - eyeOffsetAngle) * eyeDist;
        let eye2Y = head.y + Math.sin(this.angle - eyeOffsetAngle) * eyeDist;
        
        ctx.beginPath(); ctx.arc(eye1X, eye1Y, width * 0.15, 0, Math.PI * 2); ctx.fill();
        ctx.beginPath(); ctx.arc(eye2X, eye2Y, width * 0.15, 0, Math.PI * 2); ctx.fill();

        // 畫名字
        ctx.fillStyle = "rgba(255,255,255,0.7)";
        ctx.font = "12px Arial";
        ctx.textAlign = "center";
        ctx.fillText(this.name, head.x, head.y - width - 5);
        ctx.restore();
    }
}

// --- 戰場物件生成控制 ---
function initGame() {
    foods = []; ai_bots = []; toDie = [];
    
    // 初始化玩家（讀取設定初始長度）
    player = new Snake(0, 0, "#0f4", true, RUNTIME_CONFIG.startLen);
    
    // 生成指定數量的食物與 AI
    for(let i=0; i<RUNTIME_CONFIG.foodMax; i++) spawnFood();
    for(let i=0; i<RUNTIME_CONFIG.aiCount; i++) spawnAI();
}

function spawnFood(x, y, big = false, color = null) {
    let bound = RUNTIME_CONFIG.map;
    foods.push({
        x: x ?? (Math.random() - 0.5) * bound * 2,
        y: y ?? (Math.random() - 0.5) * bound * 2,
        size: big ? Math.random() * 6 + 6 : Math.random() * 3 + 2,
        color: color ?? `hsl(${Math.random() * 360}, 100%, 65%)`,
    });
}

function spawnAI() {
    let bound = RUNTIME_CONFIG.map;
    let startLen = 150;
    if (player && player.length > 3000) startLen = player.length * (0.2 + Math.random()*0.3);
    ai_bots.push(new Snake((Math.random()-0.5)*bound*2, (Math.random()-0.5)*bound*2, `hsl(${Math.random()*360}, 80%, 60%)`, false, startLen));
}

// --- 遊戲核心渲染單一主循環 ---
function loop() {
    requestAnimationFrame(loop);
    if (!started) return;

    let currentMapSize = RUNTIME_CONFIG.map;

    // 1. 背景刷新清空
    ctx.fillStyle = "#050505";
    ctx.fillRect(0, 0, canvas.width, canvas.height);

    // 2. 鏡頭隨動平滑鎖定玩家
    camera.x += (player.body[0].x - camera.x) * 0.1;
    camera.y += (player.body[0].y - camera.y) * 0.1;
    
    let targetZoom = Math.max(0.35, 1.5 - (player.getWidth() / 45));
    camera.zoom += (targetZoom - camera.zoom) * 0.05;

    ctx.save();
    ctx.translate(canvas.width / 2, canvas.height / 2);
    ctx.scale(camera.zoom, camera.zoom);
    ctx.translate(-camera.x, -camera.y);

    // 3. 動態繪製自訂邊界網格
    ctx.strokeStyle = "#161616";
    ctx.lineWidth = 2;
    for(let x = -currentMapSize; x <= currentMapSize; x += 100) {
        ctx.beginPath(); ctx.moveTo(x, -currentMapSize); ctx.lineTo(x, currentMapSize); ctx.stroke();
    }
    for(let y = -currentMapSize; y <= currentMapSize; y += 100) {
        ctx.beginPath(); ctx.moveTo(-currentMapSize, y); ctx.lineTo(currentMapSize, y); ctx.stroke();
    }
    // 紅色死亡邊界線
    ctx.strokeStyle = "#f00"; ctx.lineWidth = 6;
    ctx.strokeRect(-currentMapSize, -currentMapSize, currentMapSize*2, currentMapSize*2);

    // 4. 滑鼠操控視角角度
    let dx = mouse.x - canvas.width / 2;
    let dy = mouse.y - canvas.height / 2;
    player.angle = Math.atan2(dy, dx);

    // 磁力吸附範圍計算
    let magnetRange = Math.min(RUNTIME_CONFIG.magnetLimit, 10 * player.length);

    // 5. 食物發光晶體與吸附判定
    for (let i = foods.length - 1; i >= 0; i--) {
        let f = foods[i];
        let fdx = player.body[0].x - f.x, fdy = player.body[0].y - f.y;
        let dist = Math.sqrt(fdx*fdx + fdy*fdy);

        if (dist < magnetRange) {
            f.x += (fdx / (dist || 1)) * 9;
            f.y += (fdy / (dist || 1)) * 9;
        }

        // 吃掉食物判定
        if (dist < player.getWidth() * 0.6) {
            player.length += f.size * 1.5;
            foods.splice(i, 1);
            spawnFood();
        } else {
            ctx.fillStyle = f.color;
            ctx.beginPath(); ctx.arc(f.x, f.y, f.size, 0, Math.PI * 2); ctx.fill();
        }
    }

    // 6. 玩家更新與撞牆判定
    player.update();
    if (Math.abs(player.body[0].x) > currentMapSize || Math.abs(player.body[0].y) > currentMapSize) player.dead = true;

    // 7. AI 決策、進食與碰撞偵測
    ai_bots.forEach(s => {
        if (Math.random() > 0.96 && foods.length > 0) {
            let target = foods[Math.floor(Math.random()*foods.length)];
            s.angle = Math.atan2(target.y - s.body[0].y, target.x - s.body[0].x);
        }
        s.update();
        
        // 判定 AI 撞牆
        if (Math.abs(s.body[0].x) > currentMapSize || Math.abs(s.body[0].y) > currentMapSize) s.dead = true;
        
        // AI 吃食物
        for (let i = foods.length - 1; i >= 0; i--) {
            let f = foods[i];
            let afdx = s.body[0].x - f.x, afdy = s.body[0].y - f.y;
            let adist = Math.sqrt(afdx*afdx + afdy*afdy);
            if (adist < s.getWidth() * 0.6) {
                s.length += f.size * 1.5;
                foods.splice(i, 1);
                spawnFood();
            }
        }

        // AI 撞擊其他蛇身檢測
        [player, ...ai_bots].forEach(other => {
            if (other === s || other.dead) return;
            let h = s.body[0];
            let minDist = (s.getWidth() + other.getWidth()) * 0.45;
            for (let j = 0; j < other.body.length; j += 10) {
                let odx = h.x - other.body[j].x, ody = h.y - other.body[j].y;
                if (odx*odx + ody*ody < minDist * minDist) s.dead = true;
            }
        });
        if (s.dead) toDie.push(s);
    });

    // 8. 玩家撞擊 AI 身體判定（玩家死亡）
    ai_bots.forEach(s => {
        let minDist = (player.getWidth() + s.getWidth()) * 0.45;
        for (let j = 0; j < s.body.length; j += 6) {
            let pdx = player.body[0].x - s.body[j].x, pdy = player.body[0].y - s.body[j].y;
            if (pdx*pdx + pdy*pdy < minDist * minDist) player.dead = true;
        }
    });

    // 9. 結算死亡與重生機制
    if (player.dead) { 
        saveRecord(player.length); 
        alert(`特訓結束！你的最終分數為: ${Math.floor(player.length)}`);
        exitToMenu(); 
        ctx.restore();
        return;
    }

    // 清理死亡 AI 並化為殘留能量食物
    toDie.forEach(s => {
        let count = Math.min(40, Math.floor(s.length/40));
        for(let k=0; k<count; k++) {
            spawnFood(s.body[0].x+(Math.random()-0.5)*250, s.body[0].y+(Math.random()-0.5)*250, true, s.color);
        }
        let index = ai_bots.indexOf(s);
        if(index > -1) {
            ai_bots.splice(index, 1); 
            spawnAI();
        }
    });
    toDie = [];

    // 10. 渲染繪製所有人
    ai_bots.forEach(a => a.draw()); 
    player.draw();

    ctx.restore();

    // 11. 右上角即時排行榜與分數刷新
    document.getElementById("cur-ui").innerText = `SCORE: ${Math.floor(player.length)}`;
    let ranking = [player, ...ai_bots].sort((a,b)=>b.length - a.length).slice(0, 8);
    document.getElementById("ui-layer").innerHTML = `<b>🏆 即時特訓排行</b><hr>` + 
        ranking.map((s,i)=>`<div style="color:${s.isPlayer?'#0f4':'#fff'}">${i+1}. ${s.name} <span style="float:right">${Math.floor(s.length)}</span></div>`).join("");
}

// --- 遊戲流程控制器 ---
function startGame() {
    let inputName = document.getElementById("player-name-input").value.trim();
    playerName = inputName || "GUEST";
    
    // 擷取設定面板的所有參數
    captureConfig();
    
    initGame();
    started = true;
    document.getElementById("menu").style.display = "none";
}

function exitToMenu() {
    if (!started) return;
    saveRecord(player.length);
    started = false;
    document.getElementById("menu").style.display = "flex";
    updatePermanentUI();
}

// --- 事件接聽與視窗大小調適 ---
window.addEventListener("keydown", e => {
    if (e.key === "Escape" || e.key === "Esc") exitToMenu();
});

window.addEventListener("mousemove", e => { 
    mouse.x = e.clientX; 
    mouse.y = e.clientY; 
});
window.addEventListener("mousedown", () => mouse.down = true);
window.addEventListener("mouseup", () => mouse.down = false);

function resize() { 
    canvas.width = window.innerWidth; 
    canvas.height = window.innerHeight; 
}
window.addEventListener('resize', resize);

// 初始化初始化執行
resize();
['map', 'food', 'ai', 'len', 'speed', 'mag'].forEach(syncVal);
updatePermanentUI();
loop(); // 全局唯一單一計時器啟動
</script>
</body>
</html>
