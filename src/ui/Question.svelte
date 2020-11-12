<script lang="ts">
    import { createEventDispatcher } from "svelte";
    import { shuffle } from "../util/shuffle";

    const dispatch = createEventDispatcher();

    export let question;
    console.log("question", question.title);

    $: correctOption = question.options[0];

    let shuffledOptions;
    $: {
        shuffledOptions = [...question.options];
        shuffle(shuffledOptions);
    };

    let selectedOption;
    function setAnswer(option) {
        if (!selectedOption) {
            selectedOption = option;
            const optionIndex = question.options.findIndex(o => o === selectedOption);
            dispatch("answer", {
                optionIndex,
                option
            });
        }
    }
</script>

<style>
    button.incorrect_answer {
        background: #c55743;
    }
    button.correct_answer {
        background: #1dbd52;
    }
</style>

<h2>{question.title}</h2>

<ul>
    {#each shuffledOptions as option}
        <li>
            <button
                on:click={(e) => setAnswer(option)}
                class:correct_answer={option === selectedOption && option === correctOption}
                class:incorrect_answer={option === selectedOption && option !== correctOption}>
                {option}
            </button>
        </li>
    {/each}
</ul>
