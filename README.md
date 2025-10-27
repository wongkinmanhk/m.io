<!DOCTYPE html>
<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>超人迪加射擊怪獸遊戲</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            -webkit-tap-highlight-color: transparent;
        }

        body {
            font-family: 'Microsoft JhengHei', Arial, sans-serif;
            background: linear-gradient(to bottom, #1a1a2e 0%, #16213e 50%, #0f3460 100%);
            overflow: hidden;
            height: 100vh;
            touch-action: none;
            user-select: none;
            -webkit-user-select: none;
        }

        #gameCanvas {
            display: block;
            margin: 0 auto;
            background: linear-gradient(to bottom, #87ceeb 0%, #4a90a4 100%);
            box-shadow: 0 0 30px rgba(0, 0, 0, 0.5);
            touch-action: none;
        }

        #gameInfo {
            position: absolute;
            top: 20px;
            left: 50%;
            transform: translateX(-50%);
            background: rgba(0, 0, 0, 0.7);
            color: white;
            padding: 15px 30px;
            border-radius: 10px;
            font-size: 20px;
            display: flex;
            gap: 30px;
            z-index: 10;
            box-shadow: 0 0 20px rgba(255, 255, 255, 0.3);
        }

        #gameInfo div {
            display: flex;
            align-items: center;
            gap: 10px;
        }

        .score {
            color: #ffd700;
            font-weight: bold;
        }

        .health {
            color: #ff4444;
            font-weight: bold;
        }

        #gameOver {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            background: rgba(0, 0, 0, 0.9);
            color: white;
            padding: 40px 60px;
            border-radius: 20px;
            text-align: center;
            display: none;
            z-index: 20;
            box-shadow: 0 0 50px rgba(255, 0, 0, 0.5);
        }

        #gameOver h1 {
            font-size: 48px;
            margin-bottom: 20px;
            color: #ff4444;
            text-shadow: 0 0 10px #ff0000;
        }

        #gameOver p {
            font-size: 24px;
            margin-bottom: 30px;
        }

        #restartBtn {
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            color: white;
            border: none;
            padding: 15px 40px;
            font-size: 20px;
            border-radius: 10px;
            cursor: pointer;
            transition: transform 0.2s;
            font-weight: bold;
        }

        #restartBtn:hover {
            transform: scale(1.1);
            box-shadow: 0 0 20px rgba(102, 126, 234, 0.8);
        }

        #startScreen {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            background: rgba(0, 0, 0, 0.9);
            color: white;
            padding: 50px 70px;
            border-radius: 20px;
            text-align: center;
            z-index: 20;
            box-shadow: 0 0 50px rgba(255, 255, 255, 0.3);
        }

        #startScreen h1 {
            font-size: 52px;
            margin-bottom: 20px;
            color: #ffd700;
            text-shadow: 0 0 20px #ff6b00;
        }

        #startScreen p {
            font-size: 18px;
            margin-bottom: 30px;
            line-height: 1.6;
        }

        #startBtn {
            background: linear-gradient(135deg, #f093fb 0%, #f5576c 100%);
            color: white;
            border: none;
            padding: 18px 50px;
            font-size: 24px;
            border-radius: 10px;
            cursor: pointer;
            transition: transform 0.2s;
            font-weight: bold;
        }

        #startBtn:hover {
            transform: scale(1.1);
            box-shadow: 0 0 30px rgba(245, 87, 108, 0.8);
        }

        /* 手機觸控按鈕 */
        #mobileControls {
            position: fixed;
            bottom: 20px;
            left: 0;
            right: 0;
            display: none;
            justify-content: space-around;
            align-items: center;
            z-index: 15;
            padding: 0 20px;
        }

        .controlBtn {
            width: 70px;
            height: 70px;
            background: rgba(255, 255, 255, 0.3);
            border: 3px solid rgba(255, 255, 255, 0.6);
            border-radius: 50%;
            color: white;
            font-size: 32px;
            display: flex;
            align-items: center;
            justify-content: center;
            cursor: pointer;
            transition: all 0.2s;
            backdrop-filter: blur(10px);
        }

        .controlBtn:active {
            transform: scale(0.9);
            background: rgba(255, 255, 255, 0.5);
        }

        #shootBtn {
            width: 80px;
            height: 80px;
            background: rgba(255, 0, 0, 0.4);
            border-color: rgba(255, 0, 0, 0.8);
        }

        #shootBtn:active {
            background: rgba(255, 0, 0, 0.6);
        }

        /* 排行榜樣式 */
        #leaderboardScreen {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            background: rgba(0, 0, 0, 0.95);
            color: white;
            padding: 40px 50px;
            border-radius: 20px;
            display: none;
            z-index: 25;
            box-shadow: 0 0 50px rgba(255, 215, 0, 0.5);
            max-width: 500px;
            width: 90%;
        }

        #leaderboardScreen h2 {
            font-size: 36px;
            margin-bottom: 25px;
            color: #ffd700;
            text-align: center;
            text-shadow: 0 0 15px #ff6b00;
        }

        .leaderboard-list {
            margin-bottom: 30px;
        }

        .leaderboard-item {
            display: flex;
            justify-content: space-between;
            padding: 12px 20px;
            margin: 8px 0;
            background: rgba(255, 255, 255, 0.1);
            border-radius: 8px;
            font-size: 18px;
        }

        .leaderboard-item.rank-1 {
            background: linear-gradient(135deg, rgba(255, 215, 0, 0.3), rgba(255, 165, 0, 0.3));
            border: 2px solid gold;
        }

        .leaderboard-item.rank-2 {
            background: linear-gradient(135deg, rgba(192, 192, 192, 0.3), rgba(169, 169, 169, 0.3));
            border: 2px solid silver;
        }

        .leaderboard-item.rank-3 {
            background: linear-gradient(135deg, rgba(205, 127, 50, 0.3), rgba(184, 115, 51, 0.3));
            border: 2px solid #cd7f32;
        }

        .rank {
            font-weight: bold;
            color: #ffd700;
            min-width: 40px;
        }

        .player-name {
            flex: 1;
            text-align: left;
            margin: 0 15px;
        }

        .player-score {
            font-weight: bold;
            color: #00ff00;
        }

        #closeLeaderboardBtn {
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            color: white;
            border: none;
            padding: 12px 35px;
            font-size: 18px;
            border-radius: 8px;
            cursor: pointer;
            transition: transform 0.2s;
            font-weight: bold;
            display: block;
            margin: 0 auto;
        }

        #closeLeaderboardBtn:hover {
            transform: scale(1.05);
            box-shadow: 0 0 20px rgba(102, 126, 234, 0.8);
        }

        /* 名稱輸入表單 */
        #nameInputScreen {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            background: rgba(0, 0, 0, 0.95);
            color: white;
            padding: 40px 50px;
            border-radius: 20px;
            display: none;
            z-index: 30;
            box-shadow: 0 0 50px rgba(255, 255, 255, 0.5);
            text-align: center;
        }

        #nameInputScreen h2 {
            font-size: 32px;
            margin-bottom: 15px;
            color: #ffd700;
        }

        #nameInputScreen p {
            font-size: 20px;
            margin-bottom: 25px;
        }

        #playerNameInput {
            width: 100%;
            padding: 15px;
            font-size: 20px;
            border: 2px solid #ffd700;
            border-radius: 8px;
            background: rgba(255, 255, 255, 0.1);
            color: white;
            text-align: center;
            margin-bottom: 20px;
        }

        #playerNameInput::placeholder {
            color: rgba(255, 255, 255, 0.5);
        }

        #submitScoreBtn {
            background: linear-gradient(135deg, #f093fb 0%, #f5576c 100%);
            color: white;
            border: none;
            padding: 15px 40px;
            font-size: 20px;
            border-radius: 10px;
            cursor: pointer;
            transition: transform 0.2s;
            font-weight: bold;
            margin-right: 10px;
        }

        #submitScoreBtn:hover {
            transform: scale(1.05);
            box-shadow: 0 0 20px rgba(245, 87, 108, 0.8);
        }

        #viewLeaderboardBtn {
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            color: white;
            border: none;
            padding: 12px 30px;
            font-size: 18px;
            border-radius: 8px;
            cursor: pointer;
            transition: transform 0.2s;
            font-weight: bold;
            margin-top: 15px;
        }

        #viewLeaderboardBtn:hover {
            transform: scale(1.05);
        }

        @media (max-width: 768px) {
            #mobileControls {
                display: flex;
            }
        }
    </style>
</head>
<body>
    <div id="startScreen">
        <h1>🦸 超人迪加射擊怪獸 🦸</h1>
        <p>
            使用 ← → 方向鍵移動<br>
            按空白鍵發射光線攻擊<br>
            消滅所有怪獸，保護地球！
        </p>
        <button id="startBtn">開始遊戲</button>
    </div>

    <div id="gameInfo">
        <div>得分: <span class="score" id="score">0</span></div>
        <div>生命值: <span class="health" id="health">100</span></div>
        <div>消滅: <span id="killed">0</span> 隻</div>
    </div>

    <div id="gameOver">
        <h1>遊戲結束！</h1>
        <p>最終得分: <span id="finalScore" class="score">0</span></p>
        <p>消滅怪獸: <span id="finalKilled">0</span> 隻</p>
        <button id="restartBtn">重新開始</button>
        <button id="viewLeaderboardBtn">查看排行榜</button>
    </div>

    <div id="nameInputScreen">
        <h2>🏆 新紀錄！</h2>
        <p>得分: <span id="newScoreDisplay" class="score">0</span></p>
        <input type="text" id="playerNameInput" placeholder="輸入您的名稱" maxlength="15">
        <div>
            <button id="submitScoreBtn">提交成績</button>
        </div>
    </div>

    <div id="leaderboardScreen">
        <h2>🏆 排行榜 🏆</h2>
        <div class="leaderboard-list" id="leaderboardList"></div>
        <button id="closeLeaderboardBtn">關閉</button>
    </div>

    <div id="mobileControls">
        <div class="controlBtn" id="leftBtn">◀</div>
        <div class="controlBtn" id="shootBtn">💥</div>
        <div class="controlBtn" id="rightBtn">▶</div>
    </div>

    <canvas id="gameCanvas"></canvas>

    <script>
        const canvas = document.getElementById('gameCanvas');
        const ctx = canvas.getContext('2d');
        
        canvas.width = 800;
        canvas.height = 600;

        // 遊戲狀態
        let gameRunning = false;
        let score = 0;
        let health = 100;
        let killed = 0;
        let animationId;
        let leaderboard = [];

        // 載入排行榜
        function loadLeaderboard() {
            const saved = localStorage.getItem('ultramanLeaderboard');
            if (saved) {
                leaderboard = JSON.parse(saved);
            }
        }

        // 儲存排行榜
        function saveLeaderboard() {
            localStorage.setItem('ultramanLeaderboard', JSON.stringify(leaderboard));
        }

        // 檢查是否進入排行榜
        function isHighScore(score) {
            if (leaderboard.length < 5) return true;
            return score > leaderboard[leaderboard.length - 1].score;
        }

        // 新增分數到排行榜
        function addToLeaderboard(name, score, killed) {
            leaderboard.push({ name, score, killed, date: new Date().toLocaleDateString() });
            leaderboard.sort((a, b) => b.score - a.score);
            leaderboard = leaderboard.slice(0, 5);
            saveLeaderboard();
        }

        // 顯示排行榜
        function showLeaderboard() {
            const list = document.getElementById('leaderboardList');
            list.innerHTML = '';
            
            if (leaderboard.length === 0) {
                list.innerHTML = '<p style="text-align: center; color: #888;">尚無紀錄</p>';
            } else {
                leaderboard.forEach((entry, index) => {
                    const item = document.createElement('div');
                    item.className = `leaderboard-item rank-${index + 1}`;
                    item.innerHTML = `
                        <span class="rank">#${index + 1}</span>
                        <span class="player-name">${entry.name}</span>
                        <span class="player-score">${entry.score}</span>
                    `;
                    list.appendChild(item);
                });
            }
            
            document.getElementById('leaderboardScreen').style.display = 'block';
        }

        // 玩家（超人迪加）
        const player = {
            x: canvas.width / 2 - 25,
            y: canvas.height - 80,
            width: 50,
            height: 60,
            speed: 7,
            moveLeft: false,
            moveRight: false
        };

        // 子彈陣列
        let bullets = [];
        const bulletSpeed = 8;
        const bulletWidth = 5;
        const bulletHeight = 20;

        // 怪獸陣列
        let monsters = [];
        const monsterSpeed = 2;
        let monsterSpawnTimer = 0;
        const monsterSpawnInterval = 80;

        // 粒子效果
        let particles = [];

        // 繪製超人迪加（更精緻版本）
        function drawPlayer() {
            const x = player.x;
            const y = player.y;
            
            // 身體主體（紫紅漸層）
            const bodyGradient = ctx.createLinearGradient(x, y, x, y + 60);
            bodyGradient.addColorStop(0, '#9B30FF');
            bodyGradient.addColorStop(1, '#7B2CBF');
            
            // 身體輪廓
            ctx.fillStyle = bodyGradient;
            ctx.beginPath();
            ctx.moveTo(x + 25, y + 20);
            ctx.lineTo(x + 15, y + 25);
            ctx.lineTo(x + 15, y + 48);
            ctx.lineTo(x + 18, y + 52);
            ctx.lineTo(x + 18, y + 65);
            ctx.lineTo(x + 23, y + 65);
            ctx.lineTo(x + 23, y + 52);
            ctx.lineTo(x + 27, y + 52);
            ctx.lineTo(x + 27, y + 65);
            ctx.lineTo(x + 32, y + 65);
            ctx.lineTo(x + 32, y + 52);
            ctx.lineTo(x + 35, y + 48);
            ctx.lineTo(x + 35, y + 25);
            ctx.closePath();
            ctx.fill();
            
            // 頭部（銀白色帶金邊）
            const headGradient = ctx.createRadialGradient(x + 25, y + 12, 2, x + 25, y + 12, 14);
            headGradient.addColorStop(0, '#FFD700');
            headGradient.addColorStop(0.7, '#E6E6FA');
            headGradient.addColorStop(1, '#C0C0C0');
            ctx.fillStyle = headGradient;
            ctx.beginPath();
            ctx.arc(x + 25, y + 12, 13, 0, Math.PI * 2);
            ctx.fill();
            
            // 頭部金色裝飾線
            ctx.strokeStyle = '#FFD700';
            ctx.lineWidth = 2;
            ctx.beginPath();
            ctx.arc(x + 25, y + 12, 13, 0, Math.PI * 2);
            ctx.stroke();
            
            // 面部銀色區域
            ctx.fillStyle = '#F0F0F0';
            ctx.beginPath();
            ctx.ellipse(x + 25, y + 13, 9, 8, 0, 0, Math.PI * 2);
            ctx.fill();
            
            // 眼睛（發光效果）
            ctx.shadowBlur = 8;
            ctx.shadowColor = '#00FFFF';
            ctx.fillStyle = '#00FFFF';
            ctx.beginPath();
            ctx.ellipse(x + 20, y + 12, 3, 4, 0, 0, Math.PI * 2);
            ctx.ellipse(x + 30, y + 12, 3, 4, 0, 0, Math.PI * 2);
            ctx.fill();
            ctx.shadowBlur = 0;
            
            // 紅色胸部能量核心
            const chestGradient = ctx.createRadialGradient(x + 25, y + 32, 2, x + 25, y + 32, 10);
            chestGradient.addColorStop(0, '#FF0000');
            chestGradient.addColorStop(0.5, '#DC143C');
            chestGradient.addColorStop(1, '#8B0000');
            ctx.fillStyle = chestGradient;
            ctx.beginPath();
            ctx.ellipse(x + 25, y + 32, 8, 12, 0, 0, Math.PI * 2);
            ctx.fill();
            
            // 胸部能量線
            ctx.strokeStyle = '#FFD700';
            ctx.lineWidth = 1.5;
            ctx.beginPath();
            ctx.moveTo(x + 20, y + 28);
            ctx.lineTo(x + 25, y + 32);
            ctx.lineTo(x + 30, y + 28);
            ctx.stroke();
            
            // 手臂
            ctx.fillStyle = '#9B30FF';
            // 左臂
            ctx.beginPath();
            ctx.moveTo(x + 15, y + 25);
            ctx.lineTo(x + 8, y + 28);
            ctx.lineTo(x + 8, y + 42);
            ctx.lineTo(x + 14, y + 42);
            ctx.closePath();
            ctx.fill();
            // 右臂
            ctx.beginPath();
            ctx.moveTo(x + 35, y + 25);
            ctx.lineTo(x + 42, y + 28);
            ctx.lineTo(x + 42, y + 42);
            ctx.lineTo(x + 36, y + 42);
            ctx.closePath();
            ctx.fill();
            
            // 金色肩膀護甲
            ctx.fillStyle = '#FFD700';
            ctx.fillRect(x + 13, y + 24, 5, 3);
            ctx.fillRect(x + 32, y + 24, 5, 3);
            
            // 腰帶
            ctx.fillStyle = '#FFD700';
            ctx.fillRect(x + 15, y + 46, 20, 3);
        }

        // 繪製怪獸（多種造型）
        function drawMonster(monster) {
            const x = monster.x;
            const y = monster.y;
            
            switch(monster.type) {
                case 'melba': // 梅爾巴型（飛行怪獸）
                    // 身體
                    ctx.fillStyle = monster.color;
                    ctx.beginPath();
                    ctx.ellipse(x + 25, y + 25, 18, 22, 0, 0, Math.PI * 2);
                    ctx.fill();
                    
                    // 頭部
                    ctx.beginPath();
                    ctx.arc(x + 25, y + 12, 15, 0, Math.PI * 2);
                    ctx.fill();
                    
                    // 尖角
                    ctx.beginPath();
                    ctx.moveTo(x + 25, y);
                    ctx.lineTo(x + 20, y - 8);
                    ctx.lineTo(x + 25, y + 5);
                    ctx.lineTo(x + 30, y - 8);
                    ctx.closePath();
                    ctx.fill();
                    
                    // 翅膀
                    ctx.fillStyle = monster.color;
                    ctx.globalAlpha = 0.7;
                    ctx.beginPath();
                    ctx.moveTo(x + 10, y + 20);
                    ctx.lineTo(x - 5, y + 15);
                    ctx.lineTo(x + 5, y + 30);
                    ctx.closePath();
                    ctx.fill();
                    ctx.beginPath();
                    ctx.moveTo(x + 40, y + 20);
                    ctx.lineTo(x + 55, y + 15);
                    ctx.lineTo(x + 45, y + 30);
                    ctx.closePath();
                    ctx.fill();
                    ctx.globalAlpha = 1;
                    
                    // 眼睛
                    ctx.shadowBlur = 10;
                    ctx.shadowColor = '#FF0000';
                    ctx.fillStyle = '#FF0000';
                    ctx.beginPath();
                    ctx.arc(x + 18, y + 10, 4, 0, Math.PI * 2);
                    ctx.arc(x + 32, y + 10, 4, 0, Math.PI * 2);
                    ctx.fill();
                    ctx.shadowBlur = 0;
                    break;
                    
                case 'golza': // 哥爾贊型（力量怪獸）
                    // 粗壯身體
                    ctx.fillStyle = monster.color;
                    ctx.fillRect(x + 10, y + 18, 30, 30);
                    
                    // 頭部
                    ctx.fillRect(x + 12, y + 5, 26, 15);
                    
                    // 尖刺背部
                    ctx.beginPath();
                    ctx.moveTo(x + 15, y + 18);
                    ctx.lineTo(x + 13, y + 10);
                    ctx.lineTo(x + 20, y + 18);
                    ctx.lineTo(x + 18, y + 8);
                    ctx.lineTo(x + 25, y + 18);
                    ctx.lineTo(x + 23, y + 10);
                    ctx.lineTo(x + 30, y + 18);
                    ctx.lineTo(x + 28, y + 8);
                    ctx.lineTo(x + 35, y + 18);
                    ctx.lineTo(x + 33, y + 10);
                    ctx.lineTo(x + 37, y + 18);
                    ctx.fill();
                    
                    // 粗壯四肢
                    ctx.fillRect(x + 5, y + 25, 8, 20);
                    ctx.fillRect(x + 37, y + 25, 8, 20);
                    
                    // 發光眼睛
                    ctx.shadowBlur = 12;
                    ctx.shadowColor = '#FFFF00';
                    ctx.fillStyle = '#FFFF00';
                    ctx.fillRect(x + 16, y + 10, 6, 4);
                    ctx.fillRect(x + 28, y + 10, 6, 4);
                    ctx.shadowBlur = 0;
                    break;
                    
                case 'kyrieloid': // 基里艾爾型（惡魔型）
                    // 修長身體
                    const bodyGradient = ctx.createLinearGradient(x, y, x, y + 50);
                    bodyGradient.addColorStop(0, monster.color);
                    bodyGradient.addColorStop(1, '#000000');
                    ctx.fillStyle = bodyGradient;
                    
                    ctx.beginPath();
                    ctx.moveTo(x + 25, y + 5);
                    ctx.lineTo(x + 18, y + 15);
                    ctx.lineTo(x + 16, y + 45);
                    ctx.lineTo(x + 34, y + 45);
                    ctx.lineTo(x + 32, y + 15);
                    ctx.closePath();
                    ctx.fill();
                    
                    // 惡魔角
                    ctx.fillStyle = '#8B0000';
                    ctx.beginPath();
                    ctx.moveTo(x + 15, y + 8);
                    ctx.lineTo(x + 10, y - 5);
                    ctx.lineTo(x + 18, y + 5);
                    ctx.closePath();
                    ctx.fill();
                    ctx.beginPath();
                    ctx.moveTo(x + 35, y + 8);
                    ctx.lineTo(x + 40, y - 5);
                    ctx.lineTo(x + 32, y + 5);
                    ctx.closePath();
                    ctx.fill();
                    
                    // 邪惡眼睛
                    ctx.shadowBlur = 15;
                    ctx.shadowColor = monster.color;
                    ctx.fillStyle = monster.color;
                    ctx.beginPath();
                    ctx.ellipse(x + 20, y + 12, 5, 3, -0.3, 0, Math.PI * 2);
                    ctx.ellipse(x + 30, y + 12, 5, 3, 0.3, 0, Math.PI * 2);
                    ctx.fill();
                    ctx.shadowBlur = 0;
                    
                    // 能量核心
                    ctx.shadowBlur = 10;
                    ctx.shadowColor = monster.color;
                    ctx.fillStyle = monster.color;
                    ctx.beginPath();
                    ctx.arc(x + 25, y + 25, 5, 0, Math.PI * 2);
                    ctx.fill();
                    ctx.shadowBlur = 0;
                    break;
            }
        }

        // 繪製子彈（光線）
        function drawBullets() {
            bullets.forEach(bullet => {
                // 光線效果
                const gradient = ctx.createLinearGradient(bullet.x, bullet.y, bullet.x, bullet.y + bulletHeight);
                gradient.addColorStop(0, '#FFFFFF');
                gradient.addColorStop(0.5, '#00FFFF');
                gradient.addColorStop(1, '#0099FF');
                
                ctx.fillStyle = gradient;
                ctx.fillRect(bullet.x - bulletWidth/2, bullet.y, bulletWidth, bulletHeight);
                
                // 發光效果
                ctx.shadowBlur = 15;
                ctx.shadowColor = '#00FFFF';
                ctx.fillRect(bullet.x - bulletWidth/2, bullet.y, bulletWidth, bulletHeight);
                ctx.shadowBlur = 0;
            });
        }

        // 創建爆炸粒子
        function createExplosion(x, y, color) {
            for (let i = 0; i < 15; i++) {
                particles.push({
                    x: x,
                    y: y,
                    vx: (Math.random() - 0.5) * 8,
                    vy: (Math.random() - 0.5) * 8,
                    life: 1,
                    color: color
                });
            }
        }

        // 繪製粒子
        function drawParticles() {
            particles.forEach((particle, index) => {
                ctx.fillStyle = particle.color;
                ctx.globalAlpha = particle.life;
                ctx.beginPath();
                ctx.arc(particle.x, particle.y, 3, 0, Math.PI * 2);
                ctx.fill();
                ctx.globalAlpha = 1;
                
                particle.x += particle.vx;
                particle.y += particle.vy;
                particle.life -= 0.02;
                
                if (particle.life <= 0) {
                    particles.splice(index, 1);
                }
            });
        }

        // 生成怪獸
        function spawnMonster() {
            const monsterTypes = [
                { type: 'melba', color: '#9400D3', width: 55, height: 50 },
                { type: 'golza', color: '#FF6347', width: 50, height: 48 },
                { type: 'kyrieloid', color: '#FF1493', width: 50, height: 50 },
                { type: 'melba', color: '#00CED1', width: 55, height: 50 },
                { type: 'golza', color: '#FFD700', width: 50, height: 48 }
            ];
            
            const monsterData = monsterTypes[Math.floor(Math.random() * monsterTypes.length)];
            
            monsters.push({
                x: Math.random() * (canvas.width - 60),
                y: -50,
                width: monsterData.width,
                height: monsterData.height,
                speed: monsterSpeed + Math.random() * 2,
                color: monsterData.color,
                type: monsterData.type
            });
        }

        // 碰撞檢測
        function checkCollision(rect1, rect2) {
            return rect1.x < rect2.x + rect2.width &&
                   rect1.x + rect1.width > rect2.x &&
                   rect1.y < rect2.y + rect2.height &&
                   rect1.y + rect1.height > rect2.y;
        }

        // 更新遊戲
        function update() {
            if (!gameRunning) return;

            // 清空畫布
            ctx.fillStyle = '#87ceeb';
            ctx.fillRect(0, 0, canvas.width, canvas.height);
            
            // 繪製雲朵
            ctx.fillStyle = 'rgba(255, 255, 255, 0.6)';
            ctx.beginPath();
            ctx.arc(100, 80, 30, 0, Math.PI * 2);
            ctx.arc(130, 80, 35, 0, Math.PI * 2);
            ctx.arc(160, 80, 30, 0, Math.PI * 2);
            ctx.fill();

            // 移動玩家
            if (player.moveLeft && player.x > 0) {
                player.x -= player.speed;
            }
            if (player.moveRight && player.x < canvas.width - player.width) {
                player.x += player.speed;
            }

            // 更新子彈
            bullets.forEach((bullet, index) => {
                bullet.y -= bulletSpeed;
                if (bullet.y < 0) {
                    bullets.splice(index, 1);
                }
            });

            // 生成怪獸
            monsterSpawnTimer++;
            if (monsterSpawnTimer > monsterSpawnInterval) {
                spawnMonster();
                monsterSpawnTimer = 0;
            }

            // 更新怪獸
            monsters.forEach((monster, mIndex) => {
                monster.y += monster.speed;
                
                // 怪獸到達底部，扣血
                if (monster.y > canvas.height) {
                    monsters.splice(mIndex, 1);
                    health -= 10;
                    document.getElementById('health').textContent = health;
                    
                    if (health <= 0) {
                        gameOver();
                    }
                }

                // 檢測子彈碰撞
                bullets.forEach((bullet, bIndex) => {
                    if (checkCollision(bullet, monster)) {
                        createExplosion(monster.x + 25, monster.y + 25, monster.color);
                        monsters.splice(mIndex, 1);
                        bullets.splice(bIndex, 1);
                        score += 100;
                        killed++;
                        document.getElementById('score').textContent = score;
                        document.getElementById('killed').textContent = killed;
                    }
                });
            });

            // 繪製所有元素
            drawParticles();
            monsters.forEach(drawMonster);
            drawBullets();
            drawPlayer();

            animationId = requestAnimationFrame(update);
        }

        // 射擊
        function shoot() {
            bullets.push({
                x: player.x + player.width / 2,
                y: player.y,
                width: bulletWidth,
                height: bulletHeight
            });
        }

        // 遊戲結束
        function gameOver() {
            gameRunning = false;
            cancelAnimationFrame(animationId);
            
            // 檢查是否進入排行榜
            if (isHighScore(score)) {
                document.getElementById('newScoreDisplay').textContent = score;
                document.getElementById('nameInputScreen').style.display = 'block';
            } else {
                document.getElementById('finalScore').textContent = score;
                document.getElementById('finalKilled').textContent = killed;
                document.getElementById('gameOver').style.display = 'block';
            }
        }

        // 重新開始
        function restart() {
            score = 0;
            health = 100;
            killed = 0;
            bullets = [];
            monsters = [];
            particles = [];
            monsterSpawnTimer = 0;
            player.x = canvas.width / 2 - 25;
            
            document.getElementById('score').textContent = score;
            document.getElementById('health').textContent = health;
            document.getElementById('killed').textContent = killed;
            document.getElementById('gameOver').style.display = 'none';
            document.getElementById('nameInputScreen').style.display = 'none';
            document.getElementById('leaderboardScreen').style.display = 'none';
            
            gameRunning = true;
            update();
        }

        // 開始遊戲
        function startGame() {
            document.getElementById('startScreen').style.display = 'none';
            gameRunning = true;
            update();
        }

        // 鍵盤事件
        document.addEventListener('keydown', (e) => {
            if (!gameRunning) return;
            
            if (e.key === 'ArrowLeft') {
                player.moveLeft = true;
            }
            if (e.key === 'ArrowRight') {
                player.moveRight = true;
            }
            if (e.key === ' ') {
                e.preventDefault();
                shoot();
            }
        });

        document.addEventListener('keyup', (e) => {
            if (e.key === 'ArrowLeft') {
                player.moveLeft = false;
            }
            if (e.key === 'ArrowRight') {
                player.moveRight = false;
            }
        });

        // 初始化
        loadLeaderboard();

        // 按鈕事件
        document.getElementById('startBtn').addEventListener('click', startGame);
        document.getElementById('restartBtn').addEventListener('click', restart);
        document.getElementById('viewLeaderboardBtn').addEventListener('click', showLeaderboard);
        document.getElementById('closeLeaderboardBtn').addEventListener('click', () => {
            document.getElementById('leaderboardScreen').style.display = 'none';
            document.getElementById('gameOver').style.display = 'block';
        });
        
        document.getElementById('submitScoreBtn').addEventListener('click', () => {
            const name = document.getElementById('playerNameInput').value.trim() || '匿名玩家';
            addToLeaderboard(name, score, killed);
            document.getElementById('nameInputScreen').style.display = 'none';
            document.getElementById('finalScore').textContent = score;
            document.getElementById('finalKilled').textContent = killed;
            document.getElementById('gameOver').style.display = 'block';
        });
        
        document.getElementById('playerNameInput').addEventListener('keypress', (e) => {
            if (e.key === 'Enter') {
                document.getElementById('submitScoreBtn').click();
            }
        });

        // 手機觸控控制
        let touchLeft = false;
        let touchRight = false;

        document.getElementById('leftBtn').addEventListener('touchstart', (e) => {
            e.preventDefault();
            player.moveLeft = true;
        });
        
        document.getElementById('leftBtn').addEventListener('touchend', (e) => {
            e.preventDefault();
            player.moveLeft = false;
        });
        
        document.getElementById('rightBtn').addEventListener('touchstart', (e) => {
            e.preventDefault();
            player.moveRight = true;
        });
        
        document.getElementById('rightBtn').addEventListener('touchend', (e) => {
            e.preventDefault();
            player.moveRight = false;
        });
        
        document.getElementById('shootBtn').addEventListener('touchstart', (e) => {
            e.preventDefault();
            if (gameRunning) shoot();
        });

        // 防止手機上的拖動
        canvas.addEventListener('touchstart', (e) => {
            e.preventDefault();
        });
        
        canvas.addEventListener('touchmove', (e) => {
            e.preventDefault();
        });
    </script>
</body>
</html>
