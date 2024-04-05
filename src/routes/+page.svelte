<script>
    let gridValues = [
        [2, 0, 0, 0],
        [0, 0, 0, 0],
        [0, 0, 0, 0],
        [0, 0, 0, 0],
    ];

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
                    while (row > 0 && gridValues[row - 1][j] === 0) {
                        gridValues[row - 1][j] = gridValues[row][j];
                        gridValues[row][j] = 0;
                        row--;
                    }
                    if (row > 0 && gridValues[row - 1][j] === gridValues[row][j]) {
                        gridValues[row - 1][j] *= 2;
                        gridValues[row][j] = 0;
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
                    while (row < gridValues.length - 1 && gridValues[row + 1][j] === 0) {
                        gridValues[row + 1][j] = gridValues[row][j];
                        gridValues[row][j] = 0;
                        row++;
                    }
                    if (row < gridValues.length - 1 && gridValues[row + 1][j] === gridValues[row][j]) {
                        gridValues[row + 1][j] *= 2;
                        gridValues[row][j] = 0;
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
                    while (col > 0 && gridValues[i][col - 1] === 0) {
                        gridValues[i][col - 1] = gridValues[i][col];
                        gridValues[i][col] = 0;
                        col--;
                    }
                    if (col > 0 && gridValues[i][col - 1] === gridValues[i][col]) {
                        gridValues[i][col - 1] *= 2;
                        gridValues[i][col] = 0;
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
                    while (col < gridValues[i].length - 1 && gridValues[i][col + 1] === 0) {
                        gridValues[i][col + 1] = gridValues[i][col];
                        gridValues[i][col] = 0;
                        col++;
                    }
                    if (col < gridValues[i].length - 1 && gridValues[i][col + 1] === gridValues[i][col]) {
                        gridValues[i][col + 1] *= 2;
                        gridValues[i][col] = 0;
                    }
                }
            }
        }
    }

    function savePreviousState() {
        console.log("Saved");
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
</script>

<main>
    <div class="outer-box" role="button" tabindex="0" on:keydown={handleKeyDown}>
        {#each gridValues as row, rowIndex}
            {#each row as cell, colIndex}
                <div class="inner-box" style="background-color: {getCellColor(cell)};">{cell === 0 ? "" : cell}</div>
            {/each}
        {/each}
    </div>
</main>

<style>
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
    }
</style>
