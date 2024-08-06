<script>
  import Cell from "./lib/Cell.svelte";
  import { onMount } from "svelte";
  const numRows = 26;
  const numCols = 50;
  const cells = [];
  let numNeighbors = [];
  let intervalPointer;

  for (let row = 0; row < numRows; row++) {
    cells.push([]);
    numNeighbors.push([]);
    for (let col = 0; col < numCols; col++) {
      cells[row].push(false);
      numNeighbors[row].push(0);
    }
  }

  const toggleCellState = (row, col) => {
    cells[row][col] = !cells[row][col];
  };

  let gameState = 0;
  const incrementGameState = () => {
    gameState += 1;
  };

  onMount(() => {
    window.addEventListener("keydown", (e) => {
      if (e.code === "Space") {
        incrementGameState();
        stepGame();
      }
    });
  });

  const getNumOfNeighbors = (row, col) => {
    let numOfNeighbors = 0;

    for (let i = -1; i <= 1; i++) {
      for (let j = -1; j <= 1; j++) {
        if (i === 0 && j === 0) continue;
        const newRow = row + i;
        const newCol = col + j;
        if (
          newRow >= 0 &&
          newRow < numRows &&
          newCol >= 0 &&
          newCol < numCols
        ) {
          numOfNeighbors += cells[newRow][newCol];
        }
      }
    }

    numNeighbors[row][col] = numOfNeighbors;
  };

  const updateCell = (row, col) => {
    const num = numNeighbors[row][col];
    if (cells[row][col]) {
      if (num === 2 || num === 3) {
        cells[row][col] = true;
      } else {
        cells[row][col] = false;
      }
    } else {
      if (num === 3) {
        cells[row][col] = true;
      }
    }
  };

  const stepGame = () => {
    for (let row = 0; row < numRows; row++) {
      for (let col = 0; col < numCols; col++) {
        getNumOfNeighbors(row, col);
      }
    }
    for (let row = 0; row < numRows; row++) {
      for (let col = 0; col < numCols; col++) {
        updateCell(row, col);
      }
    }
  };

  const resetGame = () => {
    gameState = 0;
    for (let row = 0; row < numRows; row++) {
      for (let col = 0; col < numCols; col++) {
        cells[row][col] = false;
      }
    }
    if (intervalPointer) {
      clearInterval(intervalPointer);
    }
  };

  const startGame = () => {
    intervalPointer = setInterval(() => {
      stepGame();
    }, 100);
  };
</script>

<main>
  <div class="controls">
    <span>{gameState}</span>
    <button on:click={() => resetGame()}>Reset</button>
    <button on:click={() => startGame()}>Start</button>
  </div>
  {#each cells as row, rowIdx}
    <div class="row">
      {#each row as cell, colIdx}
        <Cell alive={cell} on:click={() => toggleCellState(rowIdx, colIdx)} />
      {/each}
    </div>
  {/each}
</main>

<style>
  main {
    display: flex;
    flex-direction: column;
  }

  .row {
    display: flex;
  }

  .controls {
    display: flex;
  }
</style>
