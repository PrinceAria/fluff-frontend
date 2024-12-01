<svelte:head>
    <link rel="stylesheet" href="/src/wordle.css" />
</svelte:head>

<script lang="ts">
    import { onDestroy, onMount } from "svelte";

    let grid = [
        [{ letter: "", feedback: "" }, { letter: "", feedback: "" }, { letter: "", feedback: "" }, { letter: "", feedback: "" }, { letter: "", feedback: "" }],
        [{ letter: "", feedback: "" }, { letter: "", feedback: "" }, { letter: "", feedback: "" }, { letter: "", feedback: "" }, { letter: "", feedback: "" }],
        [{ letter: "", feedback: "" }, { letter: "", feedback: "" }, { letter: "", feedback: "" }, { letter: "", feedback: "" }, { letter: "", feedback: "" }],
        [{ letter: "", feedback: "" }, { letter: "", feedback: "" }, { letter: "", feedback: "" }, { letter: "", feedback: "" }, { letter: "", feedback: "" }],
        [{ letter: "", feedback: "" }, { letter: "", feedback: "" }, { letter: "", feedback: "" }, { letter: "", feedback: "" }, { letter: "", feedback: "" }],
        [{ letter: "", feedback: "" }, { letter: "", feedback: "" }, { letter: "", feedback: "" }, { letter: "", feedback: "" }, { letter: "", feedback: "" }],
    ];

    let guesses: string[] = [];

    let guess = 0;
    let gameState = "";
    let guessedWord: string = "";
    let selectedWord: string = "";
    let showModal = false;

    onMount(async () => {
        const res = await fetch('http://localhost:8080/api/words');
        selectedWord = await res.text();
    });

    function checkWord(word: string) {
        if (word.length !== 5 || guesses.includes(word) || word.match(/[^a-zA-Z]/)) {
            return;
        }

        guesses.push(word);

        let feedback = word.split("").map((letter, index) => {
            if (letter === selectedWord[index]) {
                return 'correct';
            } else if (selectedWord.includes(letter)) {
                return 'present';
            } else {
                return 'absent';
            }
        });

        grid[guess] = word.split("").map((m, index) => {
            return { letter: m, feedback: feedback[index] };
        });
        guess++;

        guessedWord = "";
        checkGameState(word);
    }

    function checkGameState(word: string) {
        if (word === selectedWord) {
            gameState = "Correct!";
            showModal = true;
        }

        if (guess === 6) {
            gameState = "Game Over";
            showModal = true;
        }
    }

    function resetGame() {
        grid = [
            [{ letter: "", feedback: "" }, { letter: "", feedback: "" }, { letter: "", feedback: "" }, { letter: "", feedback: "" }, { letter: "", feedback: "" }],
            [{ letter: "", feedback: "" }, { letter: "", feedback: "" }, { letter: "", feedback: "" }, { letter: "", feedback: "" }, { letter: "", feedback: "" }],
            [{ letter: "", feedback: "" }, { letter: "", feedback: "" }, { letter: "", feedback: "" }, { letter: "", feedback: "" }, { letter: "", feedback: "" }],
            [{ letter: "", feedback: "" }, { letter: "", feedback: "" }, { letter: "", feedback: "" }, { letter: "", feedback: "" }, { letter: "", feedback: "" }],
            [{ letter: "", feedback: "" }, { letter: "", feedback: "" }, { letter: "", feedback: "" }, { letter: "", feedback: "" }, { letter: "", feedback: "" }],
            [{ letter: "", feedback: "" }, { letter: "", feedback: "" }, { letter: "", feedback: "" }, { letter: "", feedback: "" }, { letter: "", feedback: "" }],
        ];
        guesses = [];
        guess = 0;
        gameState = "";
        guessedWord = "";
        showModal = false;

        onMount(async () => {
            const res = await fetch('http://localhost:8080/api/words');
            selectedWord = await res.text();
        });
    }
</script>

<div id="mainWordle">
    <h1>Wordle</h1>

    <!-- <h3>current word: {selectedWord}</h3> -->

    {#if showModal}
        <div class="modal">
            <div class="modal-content">
                <h2>{gameState}</h2>
                <h3>correct word: {selectedWord}</h3>
            </div>
        </div>
    {/if}

    <div id="board">
        {#each grid as row}
            <div class="row">
                {#each row as { letter, feedback }}
                    <div class={feedback}>
                        {letter}
                    </div>
                {/each}
            </div>
        {/each}

        <input type="text" bind:value={guessedWord} maxlength="5" on:keydown={e => e.key === 'Enter' && checkWord(guessedWord)}>
        <div>
            <button on:click={() => checkWord(guessedWord)} disabled={guessedWord.length !== 5}>Submit</button>
            <button on:click={resetGame}>Play Again</button>
        </div>

    </div>
</div>