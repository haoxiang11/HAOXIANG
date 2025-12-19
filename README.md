<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>数独游戏 - 标准与异型模式</title>
    <style>
        * {
            box-sizing: border-box;
            margin: 0;
            padding: 0;
            font-family: 'Arial', sans-serif;
            -webkit-tap-highlight-color: transparent;
            -webkit-touch-callout: none;
            -webkit-user-select: none;
            user-select: none;
        }
        
        body {
            background-color: #f5f5f5;
            color: #333;
            line-height: 1.6;
            padding: 5px;
            max-width: 100%;
            margin: 0 auto;
            min-height: 100vh;
            display: flex;
            flex-direction: column;
            overflow-x: hidden;
        }
        
        /* 开始页面样式 */
        .start-screen {
            background-color: white;
            border-radius: 10px;
            box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
            padding: 15px;
            text-align: center;
            width: 100%;
            margin-top: 5px;
        }
        
        .start-screen h1 {
            color: #2c3e50;
            font-size: 1.8rem;
            margin-bottom: 15px;
        }
        
        .rules {
            text-align: left;
            margin: 15px 0;
            padding: 12px;
            background-color: #f8f9fa;
            border-radius: 8px;
            font-size: 0.9rem;
        }
        
        .rules h2 {
            color: #3498db;
            margin-bottom: 8px;
            text-align: center;
            font-size: 1.1rem;
        }
        
        .rules ul {
            padding-left: 18px;
            margin-bottom: 10px;
        }
        
        .rules li {
            margin-bottom: 6px;
        }
        
        /* 新增：游戏模式选择器 */
        .mode-selector {
            margin: 15px 0;
            display: flex;
            gap: 8px;
        }
        
        .mode-btn {
            flex: 1;
            padding: 12px;
            border: 1px solid #ddd;
            border-radius: 5px;
            background-color: #f8f9fa;
            color: #495057;
            font-weight: bold;
            cursor: pointer;
            transition: all 0.2s;
            min-height: 50px;
            font-size: 1rem;
        }
        
        .mode-btn.active {
            background-color: #9b59b6;
            color: white;
            border-color: #9b59b6;
        }
        
        .difficulty-selector {
            margin: 15px 0;
            display: flex;
            gap: 8px;
        }
        
        .difficulty-btn {
            flex: 1;
            padding: 12px;
            border: 1px solid #ddd;
            border-radius: 5px;
            background-color: #f8f9fa;
            color: #495057;
            font-weight: bold;
            cursor: pointer;
            transition: all 0.2s;
            min-height: 50px;
            font-size: 1rem;
        }
        
        .difficulty-btn.active {
            background-color: #3498db;
            color: white;
            border-color: #3498db;
        }
        
        .start-btn {
            padding: 14px 25px;
            background-color: #3498db;
            color: white;
            border: none;
            border-radius: 5px;
            font-size: 1.1rem;
            cursor: pointer;
            transition: background-color 0.2s;
            margin-top: 10px;
            min-height: 50px;
            width: 100%;
            max-width: 200px;
        }
        
        .start-btn:hover {
            background-color: #2980b9;
        }
        
        /* 游戏页面样式 - 进一步增大尺寸 */
        .game-screen {
            display: none;
            background-color: white;
            border-radius: 10px;
            box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
            padding: 8px;
            margin-bottom: 8px;
            width: 100%;
            min-height: 95vh;
            justify-content: space-between;
            flex-direction: column;
        }
        
        .game-header {
            display: flex;
            align-items: center;
            justify-content: space-between;
            margin-bottom: 8px;
            padding: 0 2px;
        }
        
        .home-btn {
            background: none;
            border: none;
            font-size: 1rem;
            cursor: pointer;
            color: #3498db;
            padding: 8px 12px;
            border-radius: 5px;
            transition: background-color 0.2s;
            min-height: 44px;
            min-width: 44px;
        }
        
        .home-btn:hover {
            background-color: #f0f0f0;
        }
        
        .game-info {
            display: flex;
            justify-content: space-between;
            margin-bottom: 8px;
            font-size: 1rem;
            width: 100%;
            padding: 0 2px;
        }
        
        .sudoku-board {
            display: grid;
            grid-template-columns: repeat(9, 1fr);
            grid-gap: 2px;
            background-color: #333;
            border: 2px solid #333;
            margin-bottom: 12px;
            border-radius: 5px;
            overflow: hidden;
            width: 100%;
            aspect-ratio: 1 / 1;
            max-width: 100%;
            margin-left: auto;
            margin-right: auto;
        }
        
        .cell {
            background-color: white;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 1.8rem;
            font-weight: bold;
            cursor: pointer;
            position: relative;
        }
        
        /* 异型数独区域样式 */
        .cell.region-0 { background-color: #f0f8ff; }
        .cell.region-1 { background-color: #f0fff0; }
        .cell.region-2 { background-color: #fff0f5; }
        .cell.region-3 { background-color: #f0f0ff; }
        .cell.region-4 { background-color: #fff8f0; }
        .cell.region-5 { background-color: #f8fff0; }
        .cell.region-6 { background-color: #f0ffff; }
        .cell.region-7 { background-color: #fff0f0; }
        .cell.region-8 { background-color: #f8f0ff; }
        
        .cell.prefilled {
            background-color: #f0f0f0;
            color: #000;
        }
        
        .cell.selected {
            background-color: #d6eaf8;
            opacity: 0.8;
        }
        
        .cell.same-number {
            background-color: #e8f4fc;
        }
        
        .cell.error {
            background-color: #fadbd8;
            color: #c0392b;
        }
        
        .cell.border-right-thick {
            border-right: 2px solid #333;
        }
        
        .cell.border-bottom-thick {
            border-bottom: 2px solid #333;
        }
        
        /* 标记样式 - 小数字显示在左上角 */
        .pencil-marks {
            position: absolute;
            top: 2px;
            left: 2px;
            display: flex;
            flex-wrap: wrap;
            max-width: 100%;
            pointer-events: none;
            gap: 1px;
            width: calc(100% - 4px);
        }
        
        .pencil-mark {
            font-size: 0.8rem;
            color: #6c757d;
            line-height: 1;
            padding: 0 1px;
            flex: 1 0 30%;
            text-align: center;
        }
        
        .number-pad {
            display: grid;
            grid-template-columns: repeat(5, 1fr);
            grid-gap: 6px;
            margin-bottom: 12px;
            padding: 0 2px;
        }
        
        .number-btn, .action-btn {
            display: flex;
            align-items: center;
            justify-content: center;
            background-color: #3498db;
            color: white;
            border: none;
            border-radius: 5px;
            font-size: 1.5rem;
            font-weight: bold;
            cursor: pointer;
            transition: background-color 0.2s;
            min-height: 60px;
        }
        
        .number-btn:hover, .action-btn:hover {
            background-color: #2980b9;
        }
        
        .action-btn {
            background-color: #e74c3c;
        }
        
        .action-btn:hover {
            background-color: #c0392b;
        }
        
        .action-btn:disabled {
            background-color: #95a5a6;
            cursor: not-allowed;
        }
        
        .action-btn:disabled:hover {
            background-color: #95a5a6;
        }
        
        .game-controls {
            display: grid;
            grid-template-columns: repeat(2, 1fr);
            grid-gap: 8px;
            margin-bottom: 12px;
            padding: 0 2px;
        }
        
        .control-btn {
            padding: 15px;
            background-color: #34495e;
            color: white;
            border: none;
            border-radius: 5px;
            font-size: 1rem;
            cursor: pointer;
            transition: background-color 0.2s;
            min-height: 60px;
        }
        
        .control-btn:hover {
            background-color: #2c3e50;
        }
        
        .control-btn.hint {
            background-color: #f39c12;
        }
        
        .control-btn.hint:hover {
            background-color: #d35400;
        }
        
        .control-btn.mark {
            background-color: #9b59b6;
        }
        
        .control-btn.mark:hover {
            background-color: #8e44ad;
        }
        
        .control-btn.mark.active {
            background-color: #8e44ad;
            box-shadow: 0 0 0 2px white, 0 0 0 4px #9b59b6;
        }
        
        .control-btn:disabled {
            background-color: #95a5a6;
            cursor: not-allowed;
        }
        
        .control-btn:disabled:hover {
            background-color: #95a5a6;
        }
        
        .game-stats {
            display: flex;
            justify-content: space-between;
            margin-top: 8px;
            font-size: 0.9rem;
            color: #6c757d;
            padding: 0 2px;
        }
        
        /* 游戏记录样式 */
        .records-screen {
            display: none;
            background-color: white;
            border-radius: 10px;
            box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
            padding: 15px;
            text-align: center;
            width: 100%;
            margin-top: 5px;
        }
        
        .records-screen h1 {
            color: #2c3e50;
            font-size: 1.8rem;
            margin-bottom: 15px;
        }
        
        .records-tabs {
            display: flex;
            margin-bottom: 15px;
            border-bottom: 1px solid #ddd;
        }
        
        .records-tab {
            flex: 1;
            padding: 12px;
            background: none;
            border: none;
            cursor: pointer;
            font-size: 1rem;
            transition: all 0.2s;
            min-height: 50px;
        }
        
        .records-tab.active {
            border-bottom: 3px solid #3498db;
            color: #3498db;
            font-weight: bold;
        }
        
        .records-list {
            text-align: left;
            margin-bottom: 15px;
            max-height: 300px;
            overflow-y: auto;
        }
        
        .records-item {
            padding: 12px;
            border-bottom: 1px solid #eee;
            display: flex;
            justify-content: space-between;
            font-size: 1rem;
        }
        
        .records-item:nth-child(odd) {
            background-color: #f8f9fa;
        }
        
        .back-btn {
            padding: 14px 25px;
            background-color: #3498db;
            color: white;
            border: none;
            border-radius: 5px;
            font-size: 1.1rem;
            cursor: pointer;
            transition: background-color 0.2s;
            margin-top: 10px;
            min-height: 50px;
            width: 100%;
            max-width: 200px;
        }
        
        .back-btn:hover {
            background-color: #2980b9;
        }
        
        /* 新增样式：数字显示区域 */
        .cell-number {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 1.8rem;
            font-weight: bold;
            z-index: 1;
        }
        
        /* 完成弹窗样式 */
        .completion-modal {
            display: none;
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background-color: rgba(0, 0, 0, 0.7);
            z-index: 1000;
            align-items: center;
            justify-content: center;
            padding: 15px;
        }
        
        .completion-content {
            background-color: white;
            border-radius: 10px;
            padding: 25px;
            text-align: center;
            width: 100%;
            max-width: 350px;
            box-shadow: 0 4px 20px rgba(0, 0, 0, 0.2);
        }
        
        .completion-content h2 {
            color: #2c3e50;
            margin-bottom: 12px;
            font-size: 1.5rem;
        }
        
        .completion-content p {
            margin-bottom: 15px;
            font-size: 1rem;
            color: #555;
        }
        
        .completion-time {
            font-size: 1.2rem;
            font-weight: bold;
            color: #3498db;
            margin: 12px 0;
        }
        
        .completion-buttons {
            display: flex;
            gap: 12px;
            margin-top: 20px;
        }
        
        .completion-btn {
            flex: 1;
            padding: 12px;
            border: none;
            border-radius: 5px;
            font-size: 0.9rem;
            cursor: pointer;
            transition: all 0.2s;
            min-height: 44px;
        }
        
        .completion-new-game {
            background-color: #3498db;
            color: white;
        }
        
        .completion-new-game:hover {
            background-color: #2980b9;
        }
        
        .completion-home {
            background-color: #95a5a6;
            color: white;
        }
        
        .completion-home:hover {
            background-color: #7f8c8d;
        }
        
        /* 模式标签样式 */
        .mode-tag {
            position: absolute;
            top: 5px;
            right: 5px;
            background-color: #9b59b6;
            color: white;
            padding: 3px 8px;
            border-radius: 3px;
            font-size: 0.7rem;
            font-weight: bold;
            z-index: 2;
        }
        
        /* 移动端特定样式 */
        @media (max-width: 480px) {
            body {
                padding: 3px;
                justify-content: flex-start;
            }
            
            .start-screen, .game-screen, .records-screen {
                padding: 10px;
            }
            
            .start-screen h1, .records-screen h1 {
                font-size: 1.6rem;
            }
            
            .sudoku-board {
                max-width: 100%;
            }
            
            .cell {
                font-size: 1.6rem;
            }
            
            .cell-number {
                font-size: 1.6rem;
            }
            
            .pencil-mark {
                font-size: 0.7rem;
            }
            
            .number-btn, .action-btn {
                font-size: 1.4rem;
                min-height: 55px;
            }
            
            .control-btn {
                font-size: 0.9rem;
                min-height: 55px;
                padding: 12px;
            }
            
            .game-info, .game-stats {
                font-size: 0.9rem;
            }
            
            .completion-content {
                padding: 20px;
            }
            
            .completion-content h2 {
                font-size: 1.3rem;
            }
            
            .completion-time {
                font-size: 1.1rem;
            }
        }
        
        @media (max-width: 360px) {
            .cell {
                font-size: 1.5rem;
            }
            
            .cell-number {
                font-size: 1.5rem;
            }
            
            .pencil-mark {
                font-size: 0.6rem;
            }
            
            .number-btn, .action-btn {
                font-size: 1.3rem;
                min-height: 52px;
            }
            
            .control-btn {
                font-size: 0.85rem;
                padding: 10px;
            }
            
            .difficulty-btn, .start-btn, .back-btn {
                min-height: 48px;
                font-size: 0.95rem;
            }
        }
        
        /* 横屏优化 */
        @media (max-height: 500px) and (orientation: landscape) {
            body {
                padding: 3px;
                justify-content: flex-start;
            }
            
            .game-screen {
                padding: 6px;
            }
            
            .sudoku-board {
                margin-bottom: 8px;
                max-height: 50vh;
            }
            
            .game-controls {
                grid-template-columns: repeat(4, 1fr);
            }
            
            .number-pad {
                grid-template-columns: repeat(10, 1fr);
            }
        }
        
        /* 防止iOS Safari缩放 */
        @supports (-webkit-touch-callout: none) {
            body {
                -webkit-text-size-adjust: 100%;
            }
        }
    </style>
</head>
<body>
    <!-- 开始页面 -->
    <div class="start-screen" id="start-screen">
        <h1>数独游戏 - 标准与异型模式</h1>
        <div class="rules">
            <h2>游戏规则</h2>
            <ul>
                <li><strong>标准数独</strong>：9x9网格，分为9个规则的3x3小宫格</li>
                <li><strong>异型数独</strong>：9x9网格，分为9个不规则形状的宫格</li>
                <li>目标都是用数字1-9填满整个网格</li>
                <li>每行、每列和每个宫格都必须包含数字1-9，且不能重复</li>
                <li>游戏开始时，部分格子已经填好数字，这些是固定数字不能更改</li>
            </ul>
            <h2>操作说明</h2>
            <ul>
                <li>点击空白格子选中它</li>
                <li>使用数字键盘输入数字</li>
                <li>使用"标记"功能可以在格子中添加候选数字</li>
                <li>使用"提示"功能可以显示当前格子的正确答案（最多3次）</li>
                <li>使用"新游戏"可以开始新的游戏</li>
                <li>使用"解题"可以显示完整答案</li>
            </ul>
        </div>
        
        <!-- 新增：游戏模式选择 -->
        <div class="mode-selector">
            <button class="mode-btn active" data-mode="standard">标准数独</button>
            <button class="mode-btn" data-mode="irregular">异型数独</button>
        </div>
        
        <div class="difficulty-selector">
            <button class="difficulty-btn active" data-difficulty="easy">简单</button>
            <button class="difficulty-btn" data-difficulty="medium">中等</button>
            <button class="difficulty-btn" data-difficulty="hard">困难</button>
        </div>
        <button class="start-btn" id="start-btn">开始游戏</button>
        <button class="start-btn" id="records-btn" style="background-color: #9b59b6; margin-left: 0; margin-top: 10px;">游戏记录</button>
    </div>

    <!-- 游戏页面 -->
    <div class="game-screen" id="game-screen">
        <div class="game-header">
            <button class="home-btn" id="home-btn">主页</button>
            <div class="game-info">
                <div class="timer">时间: <span id="timer">00:00</span></div>
                <div class="mistakes">错误: <span id="mistakes">0</span>/3</div>
            </div>
            <div style="width: 44px;"></div> <!-- 占位符，保持布局平衡 -->
        </div>
        
        <div class="sudoku-board" id="board">
            <!-- 数独棋盘将通过JavaScript生成 -->
        </div>
        
        <div class="number-pad">
            <button class="number-btn" data-number="1">1</button>
            <button class="number-btn" data-number="2">2</button>
            <button class="number-btn" data-number="3">3</button>
            <button class="number-btn" data-number="4">4</button>
            <button class="action-btn" id="delete-btn">删除</button>
            <button class="number-btn" data-number="5">5</button>
            <button class="number-btn" data-number="6">6</button>
            <button class="number-btn" data-number="7">7</button>
            <button class="number-btn" data-number="8">8</button>
            <button class="number-btn" data-number="9">9</button>
        </div>
        
        <div class="game-controls">
            <button class="control-btn hint" id="hint-btn">提示 (3/3)</button>
            <button class="control-btn mark" id="mark-btn">标记</button>
            <button class="control-btn" id="new-game-btn">新游戏</button>
            <button class="control-btn" id="solve-btn">解题</button>
        </div>
        
        <div class="game-stats">
            <div>已用提示: <span id="hints-used">0</span>/3</div>
            <div>标记模式: <span id="mark-status">关闭</span></div>
            <div>模式: <span id="game-mode-display">标准</span></div>
        </div>
    </div>

    <!-- 游戏记录页面 -->
    <div class="records-screen" id="records-screen">
        <h1>游戏记录</h1>
        <div class="records-tabs">
            <button class="records-tab active" data-difficulty="easy">简单</button>
            <button class="records-tab" data-difficulty="medium">中等</button>
            <button class="records-tab" data-difficulty="hard">困难</button>
        </div>
        <div class="records-list" id="records-list">
            <!-- 游戏记录内容将通过JavaScript生成 -->
        </div>
        <button class="back-btn" id="back-btn">返回</button>
    </div>

    <!-- 完成弹窗 -->
    <div class="completion-modal" id="completion-modal">
        <div class="completion-content">
            <h2>恭喜完成！</h2>
            <p>你成功解决了这个数独谜题</p>
            <div class="completion-time">完成时间: <span id="completion-time">00:00</span></div>
            <div class="completion-buttons">
                <button class="completion-btn completion-new-game" id="completion-new-game">新游戏</button>
                <button class="completion-btn completion-home" id="completion-home">返回主页</button>
            </div>
        </div>
    </div>

    <script>
        // 数独游戏逻辑
        document.addEventListener('DOMContentLoaded', function() {
            console.log('DOM加载完成');
            
            // 获取页面元素
            const startScreen = document.getElementById('start-screen');
            const gameScreen = document.getElementById('game-screen');
            const recordsScreen = document.getElementById('records-screen');
            const startBtn = document.getElementById('start-btn');
            const recordsBtn = document.getElementById('records-btn');
            const backBtn = document.getElementById('back-btn');
            const homeBtn = document.getElementById('home-btn');
            
            // 游戏状态变量
            let selectedCell = null;
            let mistakes = 0;
            let startTime = null;
            let timerInterval = null;
            let solution = null;
            let initialBoard = null;
            let currentDifficulty = 'easy';
            let currentMode = 'standard'; // 'standard' 或 'irregular'
            let hintsUsed = 0;
            let maxHints = 3;
            let markMode = false;
            let pencilMarks = {}; // 存储标记 {cellIndex: [marked numbers]}
            let records = {
                easy: { standard: [], irregular: [] },
                medium: { standard: [], irregular: [] },
                hard: { standard: [], irregular: [] }
            };
            let gameSolved = false;
            let regionMap = null; // 存储宫格映射关系
            
            // 异型数独的宫格定义（几种不同的异型宫格模板）
            const irregularTemplates = [
                // 模板1
                [
                    [0, 0, 0, 1, 1, 1, 2, 2, 2],
                    [0, 0, 0, 1, 1, 1, 2, 2, 2],
                    [3, 3, 3, 4, 4, 4, 5, 5, 5],
                    [3, 3, 3, 4, 4, 4, 5, 5, 5],
                    [6, 6, 6, 7, 7, 7, 8, 8, 8],
                    [6, 6, 6, 7, 7, 7, 8, 8, 8],
                    [0, 1, 3, 4, 6, 7, 2, 5, 8],
                    [0, 1, 3, 4, 6, 7, 2, 5, 8],
                    [0, 1, 3, 4, 6, 7, 2, 5, 8]
                ],
                // 模板2 - 风车形状
                [
                    [0, 0, 0, 1, 1, 2, 2, 2, 3],
                    [0, 4, 4, 1, 1, 2, 5, 5, 3],
                    [0, 4, 6, 6, 7, 7, 5, 3, 3],
                    [8, 8, 6, 6, 7, 7, 5, 5, 3],
                    [8, 8, 6, 4, 4, 7, 2, 2, 1],
                    [8, 6, 6, 4, 4, 7, 2, 1, 1],
                    [8, 5, 5, 4, 3, 3, 2, 1, 0],
                    [5, 5, 5, 4, 3, 3, 2, 0, 0],
                    [5, 5, 4, 4, 3, 2, 2, 0, 0]
                ],
                // 模板3 - 螺旋形状
                [
                    [0, 0, 0, 0, 0, 1, 1, 1, 1],
                    [8, 8, 8, 8, 0, 0, 1, 2, 2],
                    [7, 7, 7, 8, 3, 3, 3, 2, 2],
                    [6, 6, 7, 8, 3, 4, 4, 4, 2],
                    [6, 5, 7, 8, 3, 4, 5, 5, 5],
                    [6, 5, 7, 8, 3, 4, 5, 6, 6],
                    [6, 5, 7, 7, 3, 4, 5, 6, 7],
                    [6, 5, 5, 7, 3, 4, 5, 6, 7],
                    [6, 6, 6, 7, 7, 4, 5, 6, 7]
                ]
            ];
            
            // 获取DOM元素
            const boardElement = document.getElementById('board');
            const timerElement = document.getElementById('timer');
            const mistakesElement = document.getElementById('mistakes');
            const modeButtons = document.querySelectorAll('.mode-btn');
            const difficultyButtons = document.querySelectorAll('.difficulty-btn');
            const numberButtons = document.querySelectorAll('.number-btn');
            const deleteButton = document.getElementById('delete-btn');
            const hintButton = document.getElementById('hint-btn');
            const newGameButton = document.getElementById('new-game-btn');
            const solveButton = document.getElementById('solve-btn');
            const markButton = document.getElementById('mark-btn');
            const hintsUsedElement = document.getElementById('hints-used');
            const markStatusElement = document.getElementById('mark-status');
            const gameModeDisplay = document.getElementById('game-mode-display');
            const recordsTabs = document.querySelectorAll('.records-tab');
            const recordsList = document.getElementById('records-list');
            
            // 完成弹窗相关元素
            const completionModal = document.getElementById('completion-modal');
            const completionTimeElement = document.getElementById('completion-time');
            const completionNewGameBtn = document.getElementById('completion-new-game');
            const completionHomeBtn = document.getElementById('completion-home');
            
            // 加载游戏记录数据
            loadRecords();
            
            // 开始游戏按钮事件
            startBtn.addEventListener('click', function() {
                console.log('开始游戏按钮被点击，模式:', currentMode);
                startScreen.style.display = 'none';
                gameScreen.style.display = 'flex';
                initGame();
            });
            
            // 游戏记录按钮事件
            recordsBtn.addEventListener('click', function() {
                startScreen.style.display = 'none';
                recordsScreen.style.display = 'block';
                updateRecordsDisplay('easy');
            });
            
            // 返回按钮事件
            backBtn.addEventListener('click', function() {
                recordsScreen.style.display = 'none';
                startScreen.style.display = 'block';
            });
            
            // 主页按钮事件
            homeBtn.addEventListener('click', function() {
                gameScreen.style.display = 'none';
                startScreen.style.display = 'block';
                clearInterval(timerInterval);
                gameSolved = false;
            });
            
            // 模式按钮点击事件
            modeButtons.forEach(button => {
                button.addEventListener('click', function() {
                    modeButtons.forEach(btn => btn.classList.remove('active'));
                    this.classList.add('active');
                    currentMode = this.dataset.mode;
                    console.log('游戏模式切换为:', currentMode);
                });
            });
            
            // 难度按钮点击事件（在开始页面）
            difficultyButtons.forEach(button => {
                button.addEventListener('click', function() {
                    difficultyButtons.forEach(btn => btn.classList.remove('active'));
                    this.classList.add('active');
                    currentDifficulty = this.dataset.difficulty;
                });
            });
            
            // 游戏记录标签切换事件
            recordsTabs.forEach(tab => {
                tab.addEventListener('click', function() {
                    recordsTabs.forEach(t => t.classList.remove('active'));
                    this.classList.add('active');
                    updateRecordsDisplay(this.dataset.difficulty);
                });
            });
            
            // 完成弹窗按钮事件
            completionNewGameBtn.addEventListener('click', function() {
                completionModal.style.display = 'none';
                resetGame();
            });
            
            completionHomeBtn.addEventListener('click', function() {
                completionModal.style.display = 'none';
                gameScreen.style.display = 'none';
                startScreen.style.display = 'block';
                clearInterval(timerInterval);
                gameSolved = false;
            });
            
            // 新游戏按钮事件
            newGameButton.addEventListener('click', function() {
                resetGame();
            });
            
            // 解题按钮事件
            solveButton.addEventListener('click', function() {
                console.log('解题按钮被点击');
                if (!gameSolved) {
                    const cells = document.querySelectorAll('.cell');
                    for (let i = 0; i < cells.length; i++) {
                        const row = Math.floor(i / 9);
                        const col = i % 9;
                        const numberElement = cells[i].querySelector('.cell-number');
                        numberElement.textContent = solution[row][col];
                        cells[i].classList.remove('error');
                        if (initialBoard && initialBoard[row][col] === 0) {
                            cells[i].classList.add('prefilled');
                        }
                    }
                    
                    gameSolved = true;
                    disableGameControls();
                    
                    setTimeout(() => {
                        alert("已显示完整答案！游戏结束，请开始新游戏。");
                    }, 100);
                }
            });
            
            // 初始化游戏函数
            function initGame() {
                console.log('初始化游戏，模式:', currentMode);
                generateBoard();
                setupEventListeners();
                generateSudoku();
                startTimer();
                gameSolved = false;
                enableGameControls();
                gameModeDisplay.textContent = currentMode === 'standard' ? '标准' : '异型';
            }
            
            // 生成数独棋盘
            function generateBoard() {
                console.log('生成棋盘');
                boardElement.innerHTML = '';
                pencilMarks = {};
                
                // 创建81个单元格
                for (let i = 0; i < 81; i++) {
                    const cell = document.createElement('div');
                    cell.className = 'cell';
                    cell.dataset.index = i;
                    
                    // 创建数字显示区域
                    const numberElement = document.createElement('div');
                    numberElement.className = 'cell-number';
                    cell.appendChild(numberElement);
                    
                    // 创建标记区域
                    const pencilMarkContainer = document.createElement('div');
                    pencilMarkContainer.className = 'pencil-marks';
                    cell.appendChild(pencilMarkContainer);
                    
                    // 添加粗边框（标准数独才有3x3宫格边框）
                    const col = i % 9;
                    const row = Math.floor(i / 9);
                    
                    if (currentMode === 'standard') {
                        if (col === 2 || col === 5) {
                            cell.classList.add('border-right-thick');
                        }
                        
                        if (row === 2 || row === 5) {
                            cell.classList.add('border-bottom-thick');
                        }
                    }
                    
                    boardElement.appendChild(cell);
                }
                console.log('棋盘生成完成');
            }
            
            // 生成数独题目
            function generateSudoku() {
                console.log('生成数独题目，模式:', currentMode, '难度:', currentDifficulty);
                
                if (currentMode === 'standard') {
                    generateStandardSudoku();
                } else {
                    generateIrregularSudoku();
                }
            }
            
            // 生成标准数独
            function generateStandardSudoku() {
                // 使用一个更简单的方法生成数独
                solution = generateCompleteSudoku();
                initialBoard = JSON.parse(JSON.stringify(solution));
                
                // 根据难度挖空
                const emptyCells = getEmptyCellsByDifficulty();
                let positions = [];
                for (let i = 0; i < 9; i++) {
                    for (let j = 0; j < 9; j++) {
                        positions.push([i, j]);
                    }
                }
                
                shuffleArray(positions);
                
                let removed = 0;
                for (let i = 0; i < positions.length && removed < emptyCells; i++) {
                    const [row, col] = positions[i];
                    const temp = initialBoard[row][col];
                    
                    if (temp === 0) continue;
                    
                    initialBoard[row][col] = 0;
                    removed++;
                }
                
                console.log('生成了标准数独，挖空了', removed, '个单元格');
                
                // 显示初始棋盘
                displayBoard(initialBoard);
                
                // 标准数独不需要regionMap
                regionMap = null;
            }
            
            // 生成异型数独
            function generateIrregularSudoku() {
                // 随机选择一个异型模板
                const templateIndex = Math.floor(Math.random() * irregularTemplates.length);
                regionMap = irregularTemplates[templateIndex];
                
                // 生成一个完整的异型数独
                solution = generateCompleteIrregularSudoku(regionMap);
                initialBoard = JSON.parse(JSON.stringify(solution));
                
                // 根据难度挖空
                const emptyCells = getEmptyCellsByDifficulty();
                let positions = [];
                for (let i = 0; i < 9; i++) {
                    for (let j = 0; j < 9; j++) {
                        positions.push([i, j]);
                    }
                }
                
                shuffleArray(positions);
                
                let removed = 0;
                for (let i = 0; i < positions.length && removed < emptyCells; i++) {
                    const [row, col] = positions[i];
                    const temp = initialBoard[row][col];
                    
                    if (temp === 0) continue;
                    
                    // 尝试挖空，并检查是否还有唯一解
                    initialBoard[row][col] = 0;
                    
                    // 简单的唯一解检查（简化版）
                    if (hasUniqueSolution(initialBoard, regionMap)) {
                        removed++;
                    } else {
                        // 如果没有唯一解，恢复这个数字
                        initialBoard[row][col] = temp;
                    }
                }
                
                console.log('生成了异型数独，挖空了', removed, '个单元格，使用模板', templateIndex);
                
                // 显示初始棋盘
                displayBoard(initialBoard);
            }
            
            // 生成完整的异型数独
            function generateCompleteIrregularSudoku(regionMap) {
                const board = Array(9).fill().map(() => Array(9).fill(0));
                
                // 使用回溯算法生成异型数独
                const numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9];
                
                // 从第一行开始填充
                function backtrack(row, col) {
                    if (row === 9) return true; // 所有行都填完了
                    if (col === 9) return backtrack(row + 1, 0); // 这一行填完了，填下一行
                    
                    // 随机打乱数字顺序，增加随机性
                    const shuffledNumbers = [...numbers];
                    shuffleArray(shuffledNumbers);
                    
                    for (let num of shuffledNumbers) {
                        if (isValidIrregular(board, row, col, num, regionMap)) {
                            board[row][col] = num;
                            
                            if (backtrack(row, col + 1)) {
                                return true;
                            }
                            
                            board[row][col] = 0;
                        }
                    }
                    
                    return false;
                }
                
                // 启动回溯算法
                backtrack(0, 0);
                
                return board;
            }
            
            // 检查异型数独中数字是否有效
            function isValidIrregular(board, row, col, num, regionMap) {
                // 检查行
                for (let i = 0; i < 9; i++) {
                    if (board[row][i] === num) return false;
                }
                
                // 检查列
                for (let i = 0; i < 9; i++) {
                    if (board[i][col] === num) return false;
                }
                
                // 检查异型宫格
                const region = regionMap[row][col];
                for (let i = 0; i < 9; i++) {
                    for (let j = 0; j < 9; j++) {
                        if (regionMap[i][j] === region && board[i][j] === num) {
                            return false;
                        }
                    }
                }
                
                return true;
            }
            
            // 检查是否有唯一解（简化版）
            function hasUniqueSolution(board, regionMap) {
                // 简化实现：只检查是否有明显冲突
                for (let i = 0; i < 9; i++) {
                    for (let j = 0; j < 9; j++) {
                        if (board[i][j] === 0) continue;
                        
                        // 临时移除这个数字
                        const temp = board[i][j];
                        board[i][j] = 0;
                        
                        // 检查这个位置是否还能放任何数字
                        let canPlaceAny = false;
                        for (let num = 1; num <= 9; num++) {
                            if (isValidIrregular(board, i, j, num, regionMap)) {
                                canPlaceAny = true;
                                break;
                            }
                        }
                        
                        // 恢复数字
                        board[i][j] = temp;
                        
                        if (!canPlaceAny) {
                            return false;
                        }
                    }
                }
                
                return true;
            }
            
            // 生成完整的标准数独
            function generateCompleteSudoku() {
                // 创建一个空数独
                const board = Array(9).fill().map(() => Array(9).fill(0));
                
                // 使用更简单的算法生成数独
                // 先填充一个基本的数独模板
                const baseBoard = [
                    [5, 3, 4, 6, 7, 8, 9, 1, 2],
                    [6, 7, 2, 1, 9, 5, 3, 4, 8],
                    [1, 9, 8, 3, 4, 2, 5, 6, 7],
                    [8, 5, 9, 7, 6, 1, 4, 2, 3],
                    [4, 2, 6, 8, 5, 3, 7, 9, 1],
                    [7, 1, 3, 9, 2, 4, 8, 5, 6],
                    [9, 6, 1, 5, 3, 7, 2, 8, 4],
                    [2, 8, 7, 4, 1, 9, 6, 3, 5],
                    [3, 4, 5, 2, 8, 6, 1, 7, 9]
                ];
                
                // 随机交换行和列来生成不同的数独
                const shuffledBoard = shuffleSudoku(baseBoard);
                
                // 复制到board
                for (let i = 0; i < 9; i++) {
                    for (let j = 0; j < 9; j++) {
                        board[i][j] = shuffledBoard[i][j];
                    }
                }
                
                return board;
            }
            
            // 随机打乱数独（通过交换行和列）
            function shuffleSudoku(board) {
                const newBoard = JSON.parse(JSON.stringify(board));
                
                // 随机交换行（在同一宫格内）
                for (let i = 0; i < 3; i++) {
                    const rows = [i*3, i*3+1, i*3+2];
                    shuffleArray(rows);
                    
                    const tempRows = rows.map(r => [...newBoard[r]]);
                    
                    for (let j = 0; j < 3; j++) {
                        newBoard[i*3 + j] = tempRows[j];
                    }
                }
                
                // 随机交换列（在同一宫格内）
                for (let i = 0; i < 3; i++) {
                    const cols = [i*3, i*3+1, i*3+2];
                    shuffleArray(cols);
                    
                    for (let r = 0; r < 9; r++) {
                        const tempCols = cols.map(c => newBoard[r][c]);
                        for (let j = 0; j < 3; j++) {
                            newBoard[r][i*3 + j] = tempCols[j];
                        }
                    }
                }
                
                return newBoard;
            }
            
            // 根据难度获取需要挖空的单元格数量
            function getEmptyCellsByDifficulty() {
                switch(currentDifficulty) {
                    case 'easy': return 30;
                    case 'medium': return 40;
                    case 'hard': return 50;
                    default: return 30;
                }
            }
            
            // 随机打乱数组
            function shuffleArray(array) {
                for (let i = array.length - 1; i > 0; i--) {
                    const j = Math.floor(Math.random() * (i + 1));
                    [array[i], array[j]] = [array[j], array[i]];
                }
                return array;
            }
            
            // 显示棋盘
            function displayBoard(board) {
                console.log('显示棋盘');
                const cells = document.querySelectorAll('.cell');
                
                for (let i = 0; i < cells.length; i++) {
                    const row = Math.floor(i / 9);
                    const col = i % 9;
                    const value = board[row][col];
                    
                    // 清除所有样式
                    cells[i].className = 'cell';
                    
                    // 如果是异型数独，添加区域背景色
                    if (currentMode === 'irregular' && regionMap) {
                        const region = regionMap[row][col];
                        cells[i].classList.add(`region-${region}`);
                    } else if (currentMode === 'standard') {
                        // 标准数独添加粗边框
                        if (col === 2 || col === 5) {
                            cells[i].classList.add('border-right-thick');
                        }
                        
                        if (row === 2 || row === 5) {
                            cells[i].classList.add('border-bottom-thick');
                        }
                    }
                    
                    // 设置单元格内容
                    const numberElement = cells[i].querySelector('.cell-number');
                    numberElement.textContent = value === 0 ? '' : value;
                    
                    // 如果是初始棋盘中的非零数字，标记为预填充
                    if (initialBoard && initialBoard[row][col] !== 0) {
                        cells[i].classList.add('prefilled');
                    }
                    
                    // 更新标记显示
                    updatePencilMarks(i);
                }
                
                // 清除选中的单元格
                selectedCell = null;
                console.log('棋盘显示完成');
            }
            
            // 更新标记显示
            function updatePencilMarks(cellIndex) {
                const cell = document.querySelector(`.cell[data-index="${cellIndex}"]`);
                if (!cell) return;
                
                const pencilMarkContainer = cell.querySelector('.pencil-marks');
                if (!pencilMarkContainer) return;
                
                pencilMarkContainer.innerHTML = '';
                
                if (pencilMarks[cellIndex] && pencilMarks[cellIndex].length > 0) {
                    const sortedMarks = [...pencilMarks[cellIndex]].sort((a, b) => a - b);
                    
                    sortedMarks.forEach(number => {
                        const markElement = document.createElement('div');
                        markElement.className = 'pencil-mark';
                        markElement.textContent = number;
                        pencilMarkContainer.appendChild(markElement);
                    });
                }
            }
            
            // 设置事件监听器
            function setupEventListeners() {
                console.log('设置事件监听器');
                
                // 单元格点击事件
                boardElement.addEventListener('click', function(e) {
                    const cell = e.target.closest('.cell');
                    if (cell) {
                        if (!cell.classList.contains('prefilled') && !gameSolved) {
                            selectCell(cell);
                        }
                    }
                });
                
                // 数字按钮点击事件
                numberButtons.forEach(button => {
                    button.addEventListener('click', function() {
                        if (selectedCell && !gameSolved) {
                            const number = parseInt(this.dataset.number);
                            if (markMode) {
                                togglePencilMark(selectedCell, number);
                            } else {
                                inputNumber(number);
                            }
                        }
                    });
                });
                
                // 删除按钮点击事件
                deleteButton.addEventListener('click', function() {
                    if (selectedCell && !selectedCell.classList.contains('prefilled') && !gameSolved) {
                        if (markMode) {
                            clearPencilMarks(selectedCell);
                        } else {
                            const numberElement = selectedCell.querySelector('.cell-number');
                            numberElement.textContent = '';
                            selectedCell.classList.remove('error');
                        }
                    }
                });
                
                // 提示按钮点击事件
                hintButton.addEventListener('click', function() {
                    if (selectedCell && hintsUsed < maxHints && !gameSolved) {
                        const index = parseInt(selectedCell.dataset.index);
                        const row = Math.floor(index / 9);
                        const col = index % 9;
                        
                        const numberElement = selectedCell.querySelector('.cell-number');
                        numberElement.textContent = solution[row][col];
                        selectedCell.classList.remove('error');
                        hintsUsed++;
                        updateHintButton();
                        
                        if (isGameComplete()) {
                            setTimeout(() => {
                                showCompletionModal();
                            }, 500);
                        }
                    } else if (hintsUsed >= maxHints) {
                        alert('提示次数已用完！');
                    }
                });
                
                // 标记按钮点击事件
                markButton.addEventListener('click', function() {
                    if (!gameSolved) {
                        toggleMarkMode();
                    }
                });
            }
            
            // 显示完成弹窗
            function showCompletionModal() {
                clearInterval(timerInterval);
                completionTimeElement.textContent = timerElement.textContent;
                saveToRecords();
                completionModal.style.display = 'flex';
            }
            
            // 更新提示按钮状态
            function updateHintButton() {
                hintsUsedElement.textContent = `${hintsUsed}/${maxHints}`;
                hintButton.textContent = `提示 (${maxHints - hintsUsed}/${maxHints})`;
                
                if (hintsUsed >= maxHints) {
                    hintButton.disabled = true;
                } else {
                    hintButton.disabled = false;
                }
            }
            
            // 选择单元格
            function selectCell(cell) {
                if (selectedCell) {
                    selectedCell.classList.remove('selected');
                    clearHighlights();
                }
                
                selectedCell = cell;
                cell.classList.add('selected');
                
                const numberElement = cell.querySelector('.cell-number');
                highlightSameNumbers(numberElement.textContent);
            }
            
            // 高亮相同数字
            function highlightSameNumbers(number) {
                if (!number) return;
                
                const cells = document.querySelectorAll('.cell');
                cells.forEach(cell => {
                    const numberElement = cell.querySelector('.cell-number');
                    if (numberElement.textContent === number) {
                        cell.classList.add('same-number');
                    }
                });
            }
            
            // 清除高亮
            function clearHighlights() {
                const cells = document.querySelectorAll('.cell');
                cells.forEach(cell => {
                    cell.classList.remove('same-number');
                });
            }
            
            // 输入数字
            function inputNumber(number) {
                if (!selectedCell || selectedCell.classList.contains('prefilled') || gameSolved) return;
                
                const numberElement = selectedCell.querySelector('.cell-number');
                numberElement.textContent = number;
                
                // 检查输入是否正确
                const index = parseInt(selectedCell.dataset.index);
                const row = Math.floor(index / 9);
                const col = index % 9;
                
                if (currentMode === 'standard') {
                    // 标准数独验证
                    if (number !== solution[row][col]) {
                        selectedCell.classList.add('error');
                        mistakes++;
                        mistakesElement.textContent = mistakes;
                        
                        if (mistakes >= 3) {
                            setTimeout(() => {
                                alert('游戏结束！错误次数已达到3次。');
                                resetGame();
                            }, 500);
                        }
                    } else {
                        selectedCell.classList.remove('error');
                        clearPencilMarks(selectedCell);
                    }
                } else {
                    // 异型数独验证
                    const isValid = isValidIrregular(currentBoardState(), row, col, number, regionMap);
                    
                    if (!isValid || number !== solution[row][col]) {
                        selectedCell.classList.add('error');
                        mistakes++;
                        mistakesElement.textContent = mistakes;
                        
                        if (mistakes >= 3) {
                            setTimeout(() => {
                                alert('游戏结束！错误次数已达到3次。');
                                resetGame();
                            }, 500);
                        }
                    } else {
                        selectedCell.classList.remove('error');
                        clearPencilMarks(selectedCell);
                    }
                }
                
                clearHighlights();
                highlightSameNumbers(number.toString());
                
                if (isGameComplete()) {
                    setTimeout(() => {
                        showCompletionModal();
                    }, 500);
                }
            }
            
            // 获取当前棋盘状态
            function currentBoardState() {
                const board = Array(9).fill().map(() => Array(9).fill(0));
                const cells = document.querySelectorAll('.cell');
                
                for (let i = 0; i < cells.length; i++) {
                    const row = Math.floor(i / 9);
                    const col = i % 9;
                    const numberElement = cells[i].querySelector('.cell-number');
                    const value = numberElement.textContent;
                    board[row][col] = value ? parseInt(value) : 0;
                }
                
                return board;
            }
            
            // 切换标记模式
            function toggleMarkMode() {
                markMode = !markMode;
                markStatusElement.textContent = markMode ? '开启' : '关闭';
                markButton.classList.toggle('active', markMode);
            }
            
            // 切换标记
            function togglePencilMark(cell, number) {
                if (gameSolved) return;
                
                const index = parseInt(cell.dataset.index);
                
                if (!pencilMarks[index]) {
                    pencilMarks[index] = [];
                }
                
                const markIndex = pencilMarks[index].indexOf(number);
                if (markIndex === -1) {
                    pencilMarks[index].push(number);
                } else {
                    pencilMarks[index].splice(markIndex, 1);
                }
                
                updatePencilMarks(index);
            }
            
            // 清除标记
            function clearPencilMarks(cell) {
                const index = parseInt(cell.dataset.index);
                pencilMarks[index] = [];
                updatePencilMarks(index);
            }
            
            // 检查游戏是否完成
            function isGameComplete() {
                const cells = document.querySelectorAll('.cell');
                
                for (let i = 0; i < cells.length; i++) {
                    const numberElement = cells[i].querySelector('.cell-number');
                    if (!numberElement.textContent || cells[i].classList.contains('error')) {
                        return false;
                    }
                }
                
                return true;
            }
            
            // 重置游戏
            function resetGame() {
                clearInterval(timerInterval);
                mistakes = 0;
                mistakesElement.textContent = mistakes;
                hintsUsed = 0;
                updateHintButton();
                
                if (selectedCell) {
                    selectedCell.classList.remove('selected');
                    selectedCell = null;
                }
                
                markMode = false;
                markStatusElement.textContent = '关闭';
                markButton.classList.remove('active');
                pencilMarks = {};
                gameSolved = false;
                
                enableGameControls();
                generateSudoku();
                startTimer();
            }
            
            // 禁用游戏控制
            function disableGameControls() {
                numberButtons.forEach(btn => {
                    btn.disabled = true;
                    btn.style.opacity = "0.5";
                });
                deleteButton.disabled = true;
                deleteButton.style.opacity = "0.5";
                hintButton.disabled = true;
                hintButton.style.opacity = "0.5";
                markButton.disabled = true;
                markButton.style.opacity = "0.5";
                solveButton.disabled = true;
                solveButton.style.opacity = "0.5";
            }
            
            // 启用游戏控制
            function enableGameControls() {
                numberButtons.forEach(btn => {
                    btn.disabled = false;
                    btn.style.opacity = "1";
                });
                deleteButton.disabled = false;
                deleteButton.style.opacity = "1";
                hintButton.disabled = false;
                hintButton.style.opacity = "1";
                markButton.disabled = false;
                markButton.style.opacity = "1";
                solveButton.disabled = false;
                solveButton.style.opacity = "1";
            }
            
            // 开始计时器
            function startTimer() {
                startTime = new Date();
                timerInterval = setInterval(updateTimer, 1000);
                updateTimer();
            }
            
            // 更新计时器
            function updateTimer() {
                if (!startTime) return;
                
                const currentTime = new Date();
                const elapsedTime = Math.floor((currentTime - startTime) / 1000);
                const minutes = Math.floor(elapsedTime / 60);
                const seconds = elapsedTime % 60;
                
                timerElement.textContent = `${minutes.toString().padStart(2, '0')}:${seconds.toString().padStart(2, '0')}`;
            }
            
            // 加载游戏记录数据
            function loadRecords() {
                const savedRecords = localStorage.getItem('sudokuRecords');
                if (savedRecords) {
                    try {
                        records = JSON.parse(savedRecords);
                    } catch (e) {
                        console.error('解析游戏记录失败:', e);
                        records = {
                            easy: { standard: [], irregular: [] },
                            medium: { standard: [], irregular: [] },
                            hard: { standard: [], irregular: [] }
                        };
                    }
                }
            }
            
            // 保存游戏记录数据
            function saveRecords() {
                localStorage.setItem('sudokuRecords', JSON.stringify(records));
            }
            
            // 保存到游戏记录
            function saveToRecords() {
                const currentTime = timerElement.textContent;
                const timeInSeconds = convertTimeToSeconds(currentTime);
                
                // 添加到游戏记录
                records[currentDifficulty][currentMode].push({
                    time: currentTime,
                    seconds: timeInSeconds,
                    date: new Date().toLocaleDateString(),
                    mode: currentMode
                });
                
                // 按时间排序
                records[currentDifficulty][currentMode].sort((a, b) => a.seconds - b.seconds);
                
                // 只保留前10名
                if (records[currentDifficulty][currentMode].length > 10) {
                    records[currentDifficulty][currentMode] = records[currentDifficulty][currentMode].slice(0, 10);
                }
                
                saveRecords();
            }
            
            // 将时间字符串转换为秒数
            function convertTimeToSeconds(timeStr) {
                const [minutes, seconds] = timeStr.split(':').map(Number);
                return minutes * 60 + seconds;
            }
            
            // 更新游戏记录显示
            function updateRecordsDisplay(difficulty) {
                recordsList.innerHTML = '';
                
                // 合并标准和异型模式的记录
                const allRecords = [
                    ...records[difficulty].standard.map(r => ({...r, modeText: '标准'})),
                    ...records[difficulty].irregular.map(r => ({...r, modeText: '异型'}))
                ];
                
                if (allRecords.length === 0) {
                    recordsList.innerHTML = '<div class="records-item">暂无记录</div>';
                    return;
                }
                
                // 按时间排序
                allRecords.sort((a, b) => a.seconds - b.seconds);
                
                // 只显示前10名
                const topRecords = allRecords.slice(0, 10);
                
                topRecords.forEach((record, index) => {
                    const item = document.createElement('div');
                    item.className = 'records-item';
                    item.innerHTML = `
                        <div>${index + 1}. ${record.time} (${record.modeText})</div>
                        <div>${record.date}</div>
                    `;
                    recordsList.appendChild(item);
                });
            }
            
            console.log('数独游戏初始化完成');
            console.log('开始页面已显示');
        });
    </script>
</body>
</html>
