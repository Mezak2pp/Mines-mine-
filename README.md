<!DOCTYPE html>
<html lang="pt">
<head>
  <meta charset="UTF-8">
  <title>Mini Mines</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      display: flex;
      flex-direction: column;
      align-items: center;
      margin-top: 50px;
    }
    #game {
      display: grid;
      grid-template-columns: repeat(5, 50px);
      grid-template-rows: repeat(5, 50px);
      gap: 5px;
    }
    .cell {
      width: 50px;
      height: 50px;
      background-color: #ccc;
      text-align: center;
      line-height: 50px;
      font-size: 18px;
      cursor: pointer;
      border: 1px solid #999;
    }
    .revealed {
      background-color: #eee;
      cursor: default;
    }
    .mine {
      background-color: red;
      color: white;
    }
  </style>
</head>
<body>
  <h1>Mini Mines</h1>
  <div id="game"></div>
  <script>
    const gameSize = 5;
    const mineCount = 5;
    const game = document.getElementById("game");
    const cells = [];

    function createGame() {
      const mines = new Set();
      while (mines.size < mineCount) {
        const index = Math.floor(Math.random() * gameSize * gameSize);
        mines.add(index);
      }

      for (let i = 0; i < gameSize * gameSize; i++) {
        const cell = document.createElement("div");
        cell.classList.add("cell");
        cell.dataset.index = i;
        cell.dataset.mine = mines.has(i);
        cell.addEventListener("click", handleClick);
        game.appendChild(cell);
        cells.push(cell);
      }
    }

    function handleClick(e) {
      const cell = e.target;
      if (cell.classList.contains("revealed")) return;

      cell.classList.add("revealed");
      if (cell.dataset.mine === "true") {
        cell.classList.add("mine");
        cell.innerText = "💣";
        alert("Você perdeu!");
        revealAll();
      } else {
        cell.innerText = countAdjacentMines(cell) || "";
      }
    }

    function countAdjacentMines(cell) {
      const index = parseInt(cell.dataset.index);
      const x = index % gameSize;
      const y = Math.floor(index / gameSize);
      let count = 0;

      for (let dx = -1; dx <= 1; dx++) {
        for (let dy = -1; dy <= 1; dy++) {
          if (dx === 0 && dy === 0) continue;
          const nx = x + dx;
          const ny = y + dy;
          if (nx >= 0 && ny >= 0 && nx < gameSize && ny < gameSize) {
            const neighborIndex = ny * gameSize + nx;
            if (cells[neighborIndex].dataset.mine === "true") {
              count++;
            }
          }
        }
      }

      return count;
    }

    function revealAll() {
      for (const cell of cells) {
        if (cell.dataset.mine === "true") {
          cell.classList.add("revealed", "mine");
          cell.innerText = "💣";
        }
      }
    }

    createGame();
  </script>
</body>
</html>
