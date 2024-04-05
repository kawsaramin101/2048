<script>
    let gridValues = [
        [2, 0, 0, 0],
        [0, 0, 0, 0],
        [0, 0, 0, 0],
        [0, 0, 0, 0],
    ];
    let score = 0;

    function handleKeyDown(event) {
        savePreviousState();
        if (event.key === "ArrowUp" || event.key === "w" || event.key === "W") {
            up();
        } else if (event.key === "ArrowDown" || event.key === "s" || event.key === "S") {
            down();
        } else if (event.key === "ArrowLeft" || event.key === "a" || event.key === "A") {
            left();
        } else if (event.key === "ArrowRight" || event.key === "d" || event.key === "D") {
            right();
        }
        fillRandomEmptyBox();
    }

    function up() {
        for (let j = 0; j < gridValues[0].length; j++) {
            for (let i = 1; i < gridValues.length; i++) {
                if (gridValues[i][j] !== 0) {
                    let row = i;
                    let merged = false;
                    while (row > 0 && (gridValues[row - 1][j] === 0 || (gridValues[row - 1][j] === gridValues[row][j] && !merged))) {
                        if (gridValues[row - 1][j] === gridValues[row][j]) {
                            gridValues[row - 1][j] *= 2;
                            score += gridValues[row - 1][j];
                            gridValues[row][j] = 0;
                            merged = true;
                        } else {
                            gridValues[row - 1][j] = gridValues[row][j];
                            gridValues[row][j] = 0;
                        }
                        row--;
                    }
                }
            }
        }
    }

    function down() {
        for (let j = 0; j < gridValues[0].length; j++) {
            for (let i = gridValues.length - 2; i >= 0; i--) {
                if (gridValues[i][j] !== 0) {
                    let row = i;
                    let merged = false;
                    while (row < gridValues.length - 1 && (gridValues[row + 1][j] === 0 || (gridValues[row + 1][j] === gridValues[row][j] && !merged))) {
                        if (gridValues[row + 1][j] === gridValues[row][j]) {
                            gridValues[row + 1][j] *= 2;
                            score += gridValues[row + 1][j];
                            gridValues[row][j] = 0;
                            merged = true;
                        } else {
                            gridValues[row + 1][j] = gridValues[row][j];
                            gridValues[row][j] = 0;
                        }
                        row++;
                    }
                }
            }
        }
    }

    function left() {
        for (let i = 0; i < gridValues.length; i++) {
            for (let j = 1; j < gridValues[i].length; j++) {
                if (gridValues[i][j] !== 0) {
                    let col = j;
                    let merged = false;
                    while (col > 0 && (gridValues[i][col - 1] === 0 || (gridValues[i][col - 1] === gridValues[i][col] && !merged))) {
                        if (gridValues[i][col - 1] === gridValues[i][col]) {
                            gridValues[i][col - 1] *= 2;
                            score += gridValues[i][col - 1];
                            gridValues[i][col] = 0;
                            merged = true;
                        } else {
                            gridValues[i][col - 1] = gridValues[i][col];
                            gridValues[i][col] = 0;
                        }
                        col--;
                    }
                }
            }
        }
    }

    function right() {
        for (let i = 0; i < gridValues.length; i++) {
            for (let j = gridValues[i].length - 2; j >= 0; j--) {
                if (gridValues[i][j] !== 0) {
                    let col = j;
                    let merged = false;
                    while (col < gridValues[i].length - 1 && (gridValues[i][col + 1] === 0 || (gridValues[i][col + 1] === gridValues[i][col] && !merged))) {
                        if (gridValues[i][col + 1] === gridValues[i][col]) {
                            gridValues[i][col + 1] *= 2;
                            score += gridValues[i][col + 1];
                            gridValues[i][col] = 0;
                            merged = true;
                        } else {
                            gridValues[i][col + 1] = gridValues[i][col];
                            gridValues[i][col] = 0;
                        }
                        col++;
                    }
                }
            }
        }
    }

    function savePreviousState() {
        localStorage.setItem("previousState", JSON.stringify({ gridValues, score }));
    }

    function generateRandomNumber() {
        const numbers = [2, 2, 2, 2, 2, 2, 2, 2, 2, 4];
        const randomIndex = Math.floor(Math.random() * numbers.length);
        const randomNum = numbers[randomIndex];
        return randomNum;
    }

    function fillRandomEmptyBox() {
        const emptyBoxes = [];
        for (let i = 0; i < gridValues.length; i++) {
            for (let j = 0; j < gridValues[i].length; j++) {
                if (gridValues[i][j] === 0) {
                    emptyBoxes.push({ row: i, col: j });
                }
            }
        }
        if (emptyBoxes.length > 0) {
            const randomIndex = Math.floor(Math.random() * emptyBoxes.length);
            const { row, col } = emptyBoxes[randomIndex];
            gridValues[row][col] = generateRandomNumber();
        }
    }

    function getCellColor(cell) {
        switch (cell) {
            case 2:
                return "#eee4da"; // light beige
            case 4:
                return "#ede0c8"; // light brown
            case 8:
                return "#f2b179"; // light orange
            case 16:
                return "#f59563"; // light red
            case 32:
                return "#f67c5f"; // light salmon
            case 64:
                return "#f65e3b"; // orange-red
            case 128:
                return "#edcf72"; // gold
            case 256:
                return "#edcc61"; // mustard
            case 512:
                return "#edc850"; // dark yellow
            case 1024:
                return "#edc53f"; // orange-yellow
            case 2048:
                return "#edc22e"; // goldenrod
            default:
                return "#cdc1b4"; // default color for other numbers
        }
    }

    function undo() {
        const state = JSON.parse(localStorage.getItem("previousState"));
        score = state.score;
        gridValues = state.gridValues;
    }

    function newGame() {}

    function getCellAnimation(rowIndex, colIndex, newRow, newCol) {
        if (rowIndex === newRow) {
            if (colIndex > newCol) {
                return "transition: transform 0.2s ease-in-out; transform: translateX(-100%);";
            } else if (colIndex < newCol) {
                return "transition: transform 0.2s ease-in-out; transform: translateX(100%);";
            }
        } else if (colIndex === newCol) {
            if (rowIndex > newRow) {
                return "transition: transform 0.2s ease-in-out; transform: translateY(-100%);";
            } else if (rowIndex < newRow) {
                return "transition: transform 0.2s ease-in-out; transform: translateY(100%);";
            }
        }
        return ""; // No animation
    }
</script>

<main>
    <h4 class="center">Score : {score}</h4>
    <div class="outer-box" role="button" tabindex="0" on:keydown={handleKeyDown}>
        {#each gridValues as row, rowIndex}
            {#each row as cell, colIndex}
                <div class="inner-box" style="background-color: {getCellColor(cell)}; {getCellAnimation(rowIndex, colIndex)}">
                    {cell === 0 ? "" : cell}
                </div>
            {/each}
        {/each}
    </div>
    <div class="center"><button on:click={undo}>Undo</button><button on:click={newGame}>New Game</button></div>
</main>

<style>
    .center {
        margin: 10px auto;
        text-align: center;
    }
    .outer-box {
        display: grid;
        grid-template-columns: repeat(4, 1fr);
        grid-template-rows: repeat(4, 1fr);
        width: 300px;
        height: 300px;
        border: 2px solid black;
        border-radius: 5px;
        overflow: hidden;
        margin: 20px auto;
        background-color: black;
    }

    .inner-box {
        display: flex;
        justify-content: center;
        align-items: center;
        border: 2px solid black;
        box-sizing: border-box;
        font-size: 24px;
        font-weight: bold;
        border-radius: 5%;
        transition: transform 0.8s ease-in-out;
    }
    .slide-up {
        transform: translateY(-100%);
    }

    .slide-down {
        transform: translateY(100%);
    }

    .slide-left {
        transform: translateX(-100%);
    }

    .slide-right {
        transform: translateX(100%);
    }
</style>
