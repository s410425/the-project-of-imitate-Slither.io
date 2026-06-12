
<!DOCTYPE html>
<html lang="zh-Hant">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Snake.io Arena - 離線自訂特訓版</title>
    <style>
        /* 這裡放畫布、UI和控制室的 CSS 樣式... */
        body { margin: 0; overflow: hidden; background: #050505; font-family: 'Segoe UI', sans-serif; }
        canvas { display: block; }
        /* (其餘樣式請參考上一則回覆的完整程式碼) */
    </style>
</head>
<body>

    <div id="menu">
        <h1 style="color:#0f4">SNAKE.IO <span style="color:white">TRAINING</span></h1>
        <button id="start-btn" onclick="startGame()">進入特訓</button>
    </div>

    <div id="permanent-rank"><div id="p-list"></div></div>
    <div id="ui-layer"></div>
    <div id="score-ui">
        <div id="best-ui">BEST: 0</div>
        <div id="cur-ui">SCORE: 0</div>
    </div>

    <canvas id="gameCanvas"></canvas>

    <script>
        // 這裡放所有的貪食蛇與 AI 邏輯 JavaScript 程式碼...
        const canvas = document.getElementById("gameCanvas");
        const ctx = canvas.getContext("2d");
        // (其餘核心 Game Loop 請參考上一則回覆的完整程式碼)
    </script>
</body>
</html>
