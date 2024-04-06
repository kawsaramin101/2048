<script>
    import { onMount } from "svelte";

    let gridValues = [
        [0, 0, 0, 0],
        [0, 0, 0, 0],
        [0, 0, 0, 0],
        [0, 0, 0, 0],
    ];
    let score = 0;
    let gameRunning = false;
    let GameEnded = false;
    let hasWon = false;
    let highestScore = 0;

    onMount(() => {
        window.addEventListener("keydown", handleKeyDown);

        window.addEventListener('touchstart', handleTouchStart);
        window.addEventListener('touchmove', handleTouchMove);
        window.addEventListener('touchend', handleTouchEnd);
        window.addEventListener('touchcancel', handleTouchEnd); 


        if (localStorage.getItem("highestScore")) {
            highestScore = localStorage.getItem("highestScore");
        }

        return () => {
            window.removeEventListener("keydown", handleKeyDown);

            window.removeEventListener('touchstart', handleTouchStart);
            window.removeEventListener('touchmove', handleTouchMove);
            window.removeEventListener('touchend', handleTouchEnd);
            window.removeEventListener('touchcancel', handleTouchEnd);
        };
    });

    function startGame() {
        gridValues = [
            [0, 0, 0, 0],
            [0, 0, 0, 0],
            [0, 0, 0, 0],
            [0, 0, 0, 0],
        ];
        score = 0;
        gameRunning = true;
        fillRandomEmptyBox();
        localStorage.removeItem("previousState");
    }

    function handleKeyDown(event) {
        savePreviousState();
        let moved = false;
        if (event.key === "ArrowUp" || event.key === "w" || event.key === "W" || event.key === "swipeUp") {
            moved = up();
        } else if (event.key === "ArrowDown" || event.key === "s" || event.key === "S" || event.key === "swipeDown") {
            moved = down();
        } else if (event.key === "ArrowLeft" || event.key === "a" || event.key === "A" || event.key === "swipeLeft") {
            moved = left();
        } else if (event.key === "ArrowRight" || event.key === "d" || event.key === "D" || event.key === "swipeRight") {
            moved = right();
        }
        if (event.ctrlKey && event.key === "x") {
            undo();
        }

        if (moved) {
            fillRandomEmptyBox();
        }
        hasWon = check2048Box();
        if (score > highestScore) {
            highestScore = score;
            localStorage.setItem("highestScore", highestScore);
        }
        GameEnded = checkIfGameEnded();
        if (GameEnded) {
            localStorage.getItem;
        }
    }

    function up() {
        let moved = false; // Variable to track if any move or merge occurred
        for (let j = 0; j < gridValues[0].length; j++) {
            let merged = Array(gridValues.length).fill(false);
            for (let i = 1; i < gridValues.length; i++) {
                if (gridValues[i][j] !== 0) {
                    let row = i;
                    while (row > 0 && (gridValues[row - 1][j] === 0 || (gridValues[row - 1][j] === gridValues[row][j] && !merged[row - 1]))) {
                        if (gridValues[row - 1][j] === 0) {
                            gridValues[row - 1][j] = gridValues[row][j];
                            gridValues[row][j] = 0;
                            row--;
                            moved = true;
                        } else if (gridValues[row - 1][j] === gridValues[row][j] && !merged[row - 1]) {
                            gridValues[row - 1][j] *= 2;
                            score += gridValues[row - 1][j];
                            gridValues[row][j] = 0;
                            merged[row - 1] = true;
                            moved = true;
                            break;
                        }
                    }
                }
            }
        }
        return moved;
    }

    function down() {
        let moved = false;
        for (let j = 0; j < gridValues[0].length; j++) {
            let merged = Array(gridValues.length).fill(false);
            for (let i = gridValues.length - 2; i >= 0; i--) {
                if (gridValues[i][j] !== 0) {
                    let row = i;
                    while (row < gridValues.length - 1 && (gridValues[row + 1][j] === 0 || (gridValues[row + 1][j] === gridValues[row][j] && !merged[row + 1]))) {
                        if (gridValues[row + 1][j] === 0) {
                            gridValues[row + 1][j] = gridValues[row][j];
                            gridValues[row][j] = 0;
                            row++;
                            moved = true;
                        } else if (gridValues[row + 1][j] === gridValues[row][j] && !merged[row + 1]) {
                            gridValues[row + 1][j] *= 2;
                            score += gridValues[row + 1][j];
                            gridValues[row][j] = 0;
                            merged[row + 1] = true;
                            moved = true;
                            break;
                        }
                    }
                }
            }
        }
        return moved;
    }

    function left() {
        let moved = false;
        for (let i = 0; i < gridValues.length; i++) {
            let merged = Array(gridValues[i].length).fill(false);
            for (let j = 1; j < gridValues[i].length; j++) {
                if (gridValues[i][j] !== 0) {
                    let col = j;
                    while (col > 0 && (gridValues[i][col - 1] === 0 || (gridValues[i][col - 1] === gridValues[i][col] && !merged[col - 1]))) {
                        if (gridValues[i][col - 1] === 0) {
                            gridValues[i][col - 1] = gridValues[i][col];
                            gridValues[i][col] = 0;
                            col--;
                            moved = true;
                        } else if (gridValues[i][col - 1] === gridValues[i][col] && !merged[col - 1]) {
                            gridValues[i][col - 1] *= 2;
                            score += gridValues[i][col - 1];
                            gridValues[i][col] = 0;
                            merged[col - 1] = true;
                            moved = true;
                            break;
                        }
                    }
                }
            }
        }
        return moved;
    }

    function right() {
        let moved = false;
        for (let i = 0; i < gridValues.length; i++) {
            let merged = Array(gridValues[i].length).fill(false);
            for (let j = gridValues[i].length - 2; j >= 0; j--) {
                if (gridValues[i][j] !== 0) {
                    let col = j;
                    while (col < gridValues[i].length - 1 && (gridValues[i][col + 1] === 0 || (gridValues[i][col + 1] === gridValues[i][col] && !merged[col + 1]))) {
                        if (gridValues[i][col + 1] === 0) {
                            gridValues[i][col + 1] = gridValues[i][col];
                            gridValues[i][col] = 0;
                            col++;
                            moved = true;
                        } else if (gridValues[i][col + 1] === gridValues[i][col] && !merged[col + 1]) {
                            gridValues[i][col + 1] *= 2;
                            score += gridValues[i][col + 1];
                            gridValues[i][col] = 0;
                            merged[col + 1] = true;
                            moved = true;
                            break;
                        }
                    }
                }
            }
        }
        return moved;
    }

    function check2048Box() {
        for (let row of gridValues) {
            for (let cell of row) {
                if (cell === 2048) {
                    return true;
                }
            }
        }
        return false;
    }

    function checkIfGameEnded() {
        for (let row of gridValues) {
            for (let cell of row) {
                if (cell === 0) {
                    return false;
                }
            }
        }

        for (let i = 0; i < gridValues.length; i++) {
            for (let j = 0; j < gridValues[i].length; j++) {
                if (j + 1 < gridValues[i].length && gridValues[i][j] === gridValues[i][j + 1]) {
                    return false;
                }

                if (i + 1 < gridValues.length && gridValues[i][j] === gridValues[i + 1][j]) {
                    return false;
                }
            }
        }

        return true;
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
        if (state) {
            console.log(state);
            score = state.score;
            gridValues = state.gridValues;
        }
    }


    // Mobile touch gestures 

    let startX = 0;
    let startY = 0;
    let endX = 0;
    let endY = 0;

    function handleTouchStart(event) {
        startX = event.touches[0].clientX;
        startY = event.touches[0].clientY;
    }

    function handleTouchMove(event) {
        endX = event.touches[0].clientX;
        endY = event.touches[0].clientY;
    }

    function handleTouchEnd() {
        const deltaX = endX - startX;
        const deltaY = endY - startY;

        if (Math.abs(deltaX) > Math.abs(deltaY)) {
            // Horizontal swipe
            if (deltaX > 0) {
                // Swipe right
                handleKeyDown({key: "swipeRight"});
                
            } else {
                // Swipe left
               
                handleKeyDown({key: "swipeLeft"});
                
            }
        } else {
            // Vertical swipe
            if (deltaY > 0) {
                // Swipe down
                
                handleKeyDown({key: "swipeDown"});
                
            } else {
                // Swipe up
                
                handleKeyDown({key: "swipeUp"})
                

            }
        }

        // Reset touch positions
        startX = startY = endX = endY = 0;
    }


</script>

<main>
    <h4 class="center">Score : {score} || Highest Score : {highestScore}</h4>
    <div class="outer-box">
        {#each gridValues as row, rowIndex}
            {#each row as cell, colIndex}
                <div class="inner-box" style="background-color: {getCellColor(cell)};">
                    {cell === 0 ? "" : cell}
                </div>
            {/each}
        {/each}
    </div>
    <div class="center">
        {#if gameRunning}
            <button class="undo-btn" on:click={undo} title="ctrl+x">Undo </button>
            <button class="new-game-btn" on:click={startGame} title="ctrl+n">New Game</button>
        {:else}
            <button class="start-game-btn" on:click={startGame} title="ctrl+n">Start Game</button>
        {/if}
        {#if GameEnded}
            <p>Game Ended</p>
        {/if}

        {#if hasWon}
            <p>You Won</p>
        {/if}
    </div>
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
    }
    button {
        padding: 10px 20px;
        margin: 10px;
        border: none;
        border-radius: 5px;
        font-size: 16px;
        cursor: pointer;
    }

    button.undo-btn {
        background-color: #008cba; /* Blue */
        color: white;
    }

    button.undo-btn:hover {
        background-color: #0077a6; /* Darker Blue */
    }

    button.new-game-btn {
        background-color: #f44336; /* Red */
        color: white;
    }

    button.new-game-btn:hover {
        background-color: #d32f2f; /* Darker Red */
    }
</style>
