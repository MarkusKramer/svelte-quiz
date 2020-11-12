<script lang="ts">
	import { fade, fly } from 'svelte/transition';
	import { elasticOut } from 'svelte/easing';
	import questions from "./questions";
	import { answerStore } from "./stores";
	import Score from "./ui/Score.svelte";
	import Question from "./ui/Question.svelte";

	let currentQuestionIndex = 0;
	$: currentQuestion = questions[currentQuestionIndex];
	
	function handleAnswer(e) {
		const answer = e.detail;
        answerStore.update((s) => {
            s[currentQuestionIndex] = answer.optionIndex;
            return s;
		});
		
		currentQuestionIndex++;
	}

	function spin(node, { duration }) {
		return {
			duration,
			css: t => {
				const eased = elasticOut(t);
				return `
					transform: scale(${eased}) rotate(${eased * 1080}deg);
					color: hsl(
						${~~(t * 360)},
						${Math.min(100, 1000 - 1000 * t)}%,
						${Math.min(50, 500 - 500 * t)}%
					);`
			}
		};
	}
</script>

<style>
	div.carousel > div.question {
		position: absolute;
	}
	
	div.carousel > div.score {
		position: absolute;
		left: 50%;
		top: 50%;
		transform: translate(-50%,-50%);
	}

	div.carousel > div.score > span {
		position: absolute;
		transform: translate(-50%,-50%);
		font-size: 4em;
		text-align: center;
		display: block;
		width: 20rem;
	}
</style>


<h1>Quiz</h1>

<p>
	Question:
	{currentQuestionIndex + 1}
	/
	{questions.length}

	<button on:click={(e) => (currentQuestionIndex = currentQuestionIndex > 0 ? currentQuestionIndex - 1 : questions.length - 1)}>&lt;</button>
	<button on:click={(e) => (currentQuestionIndex = (currentQuestionIndex + 1) % questions.length)}>&gt;</button>
</p>

<div class="carousel">
	{#each questions as question, i }
		{#if i === currentQuestionIndex }

		<div class="question" in:fly="{{ x: 800, duration: 1000 }}" out:fade={{duration: 800}}>
			<Question question={currentQuestion} on:answer={handleAnswer} />
		</div>

		{/if}
	{/each}

	{#if currentQuestionIndex === questions.length}
		<div class="score" in:spin="{{duration: 8000}}" out:fade>
			<span><Score/></span>
		</div>
	{/if}
</div>
