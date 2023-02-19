<script lang="ts">
	import { questions } from './questions';

	let question = questions[Math.floor(Math.random() * questions.length)];
	let maxScore = 7;
	let score = 0;

	let isGameStarted = false;
	let isReady = false;
	let message = '';

	const timer = {
		isPaused: true,
		time: 5,
		timer: null
	};

	let audio;

	function startTimer() {
		timer.time = 5;
		timer.isPaused = false;
		timer.timer = setInterval(() => {
			timer.time--;
			if (timer.time < 0) {
				audio.play();
				timer.isPaused = true;
				clearInterval(timer.timer);
			}
		}, 1000);
	}

	function onCorrect() {
		score++;
		if (score >= maxScore) {
			endGame();
			return;
		}
		ready();
	}

	function onWrong() {
		ready();
	}

	function ready() {
		isReady = true;
	}

	function startRound() {
		isReady = false;
		question = questions[Math.floor(Math.random() * questions.length)];
		startTimer();
	}

	function endGame() {
		message = 'You Win!';
		isGameStarted = false;
		score = 0;
	}
</script>

<svelte:head>
	<title>Give You Five Seconds</title>
	<meta property="og:title" content="Give You Five Seconds" />
	<meta
		property="og:description"
		content="A game where you have 5 seconds to give 3 correct answers to a given question"
	/>
	<meta property="og:image" content="https://give-you-five-seconds.vercel.app/thumbnail.png" />
</svelte:head>

<div class="flex h-screen mx-2">
	<div class="m-auto text-center">
		<h1 class="{isGameStarted ? 'text-3xl' : 'text-4xl'} font-extrabold my-4">
			Give You Five Seconds
		</h1>
		{#if !isGameStarted}
			{#if message}
				<div class="my-4">
					<h2 class="text-2xl font-bold">{message}</h2>
				</div>
			{/if}
			<button
				class="btn"
				on:click={() => {
					isGameStarted = true;
					isReady = true;
				}}
				>Start
			</button>
		{:else}
			{#if isReady}
				<div class="card bg-base-100 shadow-xl my-2">
					<div class="card-body items-center text-center">
						<h2>Name 3 of</h2>
						<p class="text-2xl font-bold">?</p>
					</div>
				</div>
				<div class="my-2 h-24 flex">
					<div class="m-auto items-center text-center">
						<button class="btn" on:click={startRound}>Start</button>
					</div>
				</div>
			{:else}
				<div class="card bg-base-100 shadow-xl my-2">
					<div class="card-body items-center text-center">
						<h2>Name 3 of</h2>
						<p class="text-2xl font-bold">{question}</p>
					</div>
				</div>
				<div class="my-2 h-24 flex">
					<div class="m-auto items-center text-center">
						{#if !timer.isPaused}
							<span class="countdown font-mono text-5xl">
								<span style="--value:{timer.time};" />
							</span>
						{:else}
							<span>
								<button class="btn btn-success" on:click={onCorrect}>Correct</button>
								<button class="btn btn-error" on:click={onWrong}>Wrong</button>
							</span>
						{/if}
					</div>
				</div>
			{/if}
			<div class="my-2">
				<h2 class="text-lg font-bold">Score</h2>
				<progress class="progress w-56" value={score} max={maxScore} />
				{score}
			</div>
		{/if}
	</div>
</div>

<audio src="https://cdn.freesound.org/previews/426/426888_7913959-lq.mp3" bind:this={audio} />

<style>
	@supports (-webkit-touch-callout: none) {
		.h-screen {
			height: -webkit-fill-available;
		}
	}
</style>
