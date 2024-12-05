## Hi there 👋

<!--
**Kubad34/Kubad34** is a ✨ _special_ ✨ repository because its `README.md` (this file) appears on your GitHub profile.

Here are some ideas to get you started:

- 🔭 I’m currently working on ...
- 🌱 I’m currently learning ...
- 👯 I’m looking to collaborate on ...
- 🤔 I’m looking for help with ...
- 💬 Ask me about ...
- 📫 How to reach me: ...
- 😄 Pronouns: ...
- ⚡ Fun fact: ...
-->
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Saper (Minesweeper)</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            text-align: center;
            background-color: #f4f4f9;
            margin: 0;
            padding: 0;
        }
        h1 {
            color: #333;
        }
        #game-container {
            display: grid;
            grid-template-columns: repeat(10, 40px);
            gap: 2px;
            justify-content: center;
            margin: 20px auto;
        }
        .cell {
            width: 40px;
            height: 40px;
            background-color: #ccc;
            border: 1px solid #999;
            font-size: 18px;
            font-weight: bold;
            color: #333;
            display: flex;
            align-items: center;
            justify-content: center;
            cursor: pointer;
            user-select: none;
        }
        .cell.revealed {
            background-color: #fff;
            cursor: default;
        }
        .cell.mine {
            background-color: red;
            color: white;
        }
        .cell.flag {
            background-color: yellow;
        }
        footer {
            margin-top: 20px;
        }
    </style>
</head>
<body>
    <h1>Saper (Minesweeper)</h1>
    <p>Click cells to reveal them. Right-click to flag suspected mines!</p>
    <div id="game-container"></div>
    <footer>
        <p>© 2024 [Your Name]. All rights reserved.</p>
    </footer>
    <script>
        const rows = 10;
        const cols = 10;
        const minesCount = 15;
        const gameContainer = document.getElementById('game-container');
        let minefield = [];
        let revealedCount = 0;

        // Initialize game
        function createMinefield() {
            // Create grid structure
            minefield = Array.from({ length: rows }, () => Array(cols).fill({ mine: false, revealed: false, flagged: false, count: 0 }));

            // Place mines randomly
            let placedMines = 0;
            while (placedMines < minesCount) {
                const r = Math.floor(Math.random() * rows);
                const c = Math.floor(Math.random() * cols);
                if (!minefield[r][c].mine) {
                    minefield[r][c] = { ...minefield[r][c], mine: true };
                    placedMines++;
                }
            }

            // Calculate mine counts
            for (let r = 0; r < rows; r++) {
                for (let c = 0; c < cols; c++) {
                    if (!minefield[r][c].mine) {
                        minefield[r][c].count = getAdjacentMines(r, c);
                    }
                }
            }

            renderGrid();
        }

        // Get number of adjacent mines
        function getAdjacentMines(row, col) {
            const directions = [
                [-1, -1], [-1, 0], [-1, 1],
                [0, -1],           [0, 1],
                [1, -1], [1, 0], [1, 1]
            ];
            return directions.reduce((count, [dr, dc]) => {
                const r = row + dr, c = col + dc;
                return count + (r >= 0 && r < rows && c >= 0 && c < cols && minefield[r][c].mine ? 1 : 0);
            }, 0);
        }

        // Render the grid
        function renderGrid() {
            gameContainer.innerHTML = '';
            for (let r = 0; r < rows; r++) {
                for (let c = 0; c < cols; c++) {
                    const cell = document.createElement('div');
                    cell.classList.add('cell');
                    if (minefield[r][c].revealed) {
                        cell.classList.add('revealed');
                        if (minefield[r][c].mine) {
                            cell.classList.add('mine');
                            cell.textContent = '💣';
                        } else if (minefield[r][c].count > 0) {
                            cell.textContent = minefield[r][c].count;
                        }
                    } else if (minefield[r][c].flagged) {
                        cell.classList.add('flag');
                        cell.textContent = '🚩';
                    }
                    cell.addEventListener('click', () => handleLeftClick(r, c));
                    cell.addEventListener('contextmenu', (e) => handleRightClick(e, r, c));
                    gameContainer.appendChild(cell);
                }
            }
        }

        // Handle left click
        function handleLeftClick(row, col) {
            if (minefield[row][col].revealed || minefield[row][col].flagged) return;
            if (minefield[row][col].mine) {
                revealAllMines();
                alert('Game Over!');
            } else {
                revealCell(row, col);
                if (revealedCount === rows * cols - minesCount) {
                    alert('You Win!');
                }
            }
            renderGrid();
        }

        // Handle right click (flag)
        function handleRightClick(e, row, col) {
            e.preventDefault();
            if (minefield[row][col].revealed) return;
            minefield[row][col].flagged = !minefield[row][col].flagged;
            renderGrid();
        }

        // Reveal cell
        function revealCell(row, col) {
            if (row < 0 || row >= rows || col < 0 || col >= cols || minefield[row][col].revealed || minefield[row][col].flagged) return;
            minefield[row][col].revealed = true;
            revealedCount++;
            if (minefield[row][col].count === 0) {
                const directions = [
                    [-1, -1], [-1, 0], [-1, 1],
                    [0, -1],           [0, 1],
                    [1, -1], [1, 0], [1, 1]
                ];
                directions.forEach(([dr, dc]) => revealCell(row + dr, col + dc));
            }
        }

        // Reveal all mines
        function revealAllMines() {
            for (let r = 0; r < rows; r++) {
                for (let c = 0; c < cols; c++) {
                    if (minefield[r][c].mine) {
                        minefield[r][c].revealed = true;
                    }
                }
            }
        }

        // Start the game
        createMinefield();
    </script>
</body>
</html>
