<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <title>2인용 미니 오목</title>
    <style>
        body {
            font-family: 'Malgun Gothic', sans-serif;
            background-color: #f0f2f5;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            height: 100vh;
            margin: 0;
        }
        h1 {
            color: #333;
            margin-bottom: 5px;
        }
        #turn-indicator {
            font-size: 18px;
            font-weight: bold;
            color: #555;
            margin-bottom: 15px;
        }
        #board {
            display: grid;
            grid-template-columns: repeat(15, 30px);
            grid-template-rows: repeat(15, 30px);
            background-color: #dcb35c;
            border: 2px solid #333;
            box-shadow: 0 4px 10px rgba(0,0,0,0.2);
        }
        .cell {
            width: 30px;
            height: 30px;
            border: 1px solid #b58d3c;
            display: flex;
            align-items: center;
            justify-content: center;
            cursor: pointer;
        }
        .stone {
            width: 24px;
            height: 24px;
            border-radius: 50%;
        }
        .black {
            background-color: #000;
            box-shadow: inset 2px 2px 4px rgba(255,255,255,0.4);
        }
        .white {
            background-color: #fff;
            box-shadow: inset -2px -2px 4px rgba(0,0,0,0.4);
            border: 1px solid #ccc;
        }
        #reset-btn {
            margin-top: 20px;
            padding: 10px 20px;
            font-size: 16px;
            background-color: #4CAF50;
            color: white;
            border: none;
            border-radius: 5px;
            cursor: pointer;
        }
        #reset-btn:hover {
            background-color: #45a049;
        }
    </style>
</head>
<body>

    <h1>2인용 미니 오목</h1>
    <div id="turn-indicator">현재 차례: 흑돌 (플레이어 1)</div>
    <div id="board"></div>
    <button id="reset-btn" onclick="resetGame()">게임 다시 시작</button>

    <script>
        const BOARD_SIZE = 15;
        let board = Array(BOARD_SIZE).fill(null).map(() => Array(BOARD_SIZE).fill(0));
        let currentPlayer = 1; // 1: 흑돌, 2: 백돌
        let gameOver = false;

        const boardElement = document.getElementById('board');
        const turnIndicator = document.getElementById('turn-indicator');

        // 보드판 생성
        function createBoard() {
            boardElement.innerHTML = '';
            for (let r = 0; r < BOARD_SIZE; r++) {
                for (let c = 0; c < BOARD_SIZE; c++) {
                    const cell = document.createElement('div');
                    cell.classList.add('cell');
                    cell.dataset.row = r;
                    cell.dataset.col = c;
                    cell.addEventListener('click', handleCellClick);
                    
                    if (board[r][c] === 1) {
                        const stone = document.createElement('div');
                        stone.classList.add('stone', 'black');
                        cell.appendChild(stone);
                    } else if (board[r][c] === 2) {
                        const stone = document.createElement('div');
                        stone.classList.add('stone', 'white');
                        cell.appendChild(stone);
                    }
                    
                    boardElement.appendChild(cell);
                }
            }
        }

        // 칸을 클릭했을 때
        function handleCellClick(e) {
            if (gameOver) return;
            const r = parseInt(e.target.dataset.row);
            const c = parseInt(e.target.dataset.col);

            if (board[r][c] !== 0) return; // 이미 돌이 놓여있다면 무시

            board[r][c] = currentPlayer;
            createBoard();

            if (checkWin(r, c, currentPlayer)) {
                turnIndicator.textContent = `🎉 승리! ${currentPlayer === 1 ? '흑돌(플레이어 1)' : '백돌(플레이어 2)'} 승리!`;
                gameOver = true;
                return;
            }

            // 차례 변경
            currentPlayer = currentPlayer === 1 ? 2 : 1;
            turnIndicator.textContent = `현재 차례: ${currentPlayer === 1 ? '흑돌 (플레이어 1)' : '백돌 (플레이어 2)'}`;
        }

        // 승리 조건 확인 함수
        function checkWin(r, c, player) {
            const directions = [
                { dr: 0, dc: 1 },  // 가로
                { dr: 1, dc: 0 },  // 세로
                { dr: 1, dc: 1 },  // 대각선 우하
                { dr: 1, dc: -1 }  // 대각선 좌하
            ];

            for (let {dr, dc} of directions) {
                let count = 1;

                // 정방향 체크
                let step = 1;
                while (true) {
                    let nr = r + dr * step;
                    let nc = c + dc * step;
                    if (nr < 0 || nr >= BOARD_SIZE || nc < 0 || nc >= BOARD_SIZE || board[nr][nc] !== player) break;
                    count++;
                    step++;
                }

                // 역방향 체크
                step = 1;
                while (true) {
                    let nr = r - dr * step;
                    let nc = c - dc * step;
                    if (nr < 0 || nr >= BOARD_SIZE || nc < 0 || nc >= BOARD_SIZE || board[nr][nc] !== player) break;
                    count++;
                    step++;
                }

                if (count >= 5) return true;
            }
            return false;
        }

        // 게임 리셋
        function resetGame() {
            board = Array(BOARD_SIZE).fill(null).map(() => Array(BOARD_SIZE).fill(0));
            currentPlayer = 1;
            gameOver = false;
            turnIndicator.textContent = '현재 차례: 흑돌 (플레이어 1)';
            createBoard();
        }

        createBoard();
    </script>
</body>
</html>
