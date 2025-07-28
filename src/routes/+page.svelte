<script>
	import { GoogleGenAI } from '@google/genai';
	let generatingJoke = $state(false);
	let jokes = $state([]);
	let rating = $state('');
	let modal = $state();

	// The client gets the API key from the environment variable `GEMINI_API_KEY`.
	const ai = new GoogleGenAI({apiKey: process.env.GEMINI_API_KEY});

	async function generateJoke() {
		generatingJoke = true;
		const response = await ai.models.generateContent({
			model: 'gemini-2.5-flash',
			contents:
				'Write a 10 word summary on AI, then come up with a random new dad joke based on other dad jokes. Provide the response in plain text only, without any Markdown formatting. First give the summary, then give a line break (so that theres one empty line in between), then the joke. Make sure the whole joke is in one line.'
		});
		console.log(response.text);
		const res = response.text;
		const message = res.split('\n')[2];
		jokes.push(message);
		generatingJoke = false;
	}

	async function generateJokeResponse() {
		generatingJoke = true;
		const ctx = prompt('What did they say?');
		const response = await ai.models.generateContent({
			model: 'gemini-2.5-flash',
			contents: `Write a 10 word summary on AI, then come up with a dumb joke in response to if someone said: ${ctx}. Provide the response in plain text only, without any Markdown formatting. First give the summary, then give a line break (so that theres one empty line in between), then the joke. Make sure the whole joke is in one line.`
		});
		console.log(response.text);
		const res = response.text;
		const message = res.split('\n')[2];
		jokes.push(message);
		generatingJoke = false;
	}

	async function rateJoke() {
		const ctx = prompt('What is your joke?');
		const response = await ai.models.generateContent({
			model: 'gemini-2.5-flash',
			contents: `I gave this joke: ${ctx}. Rate the joke on a random scale, such as "five bananas out of ten ice cream cones", and give only the rating, no other information.`
		});
		console.log(response.text);
		const res = response.text;
		rating = res;
	}

	const onModalClick = (e) => {
		if (e.target == modal) {
			rating = '';
		}
	};
</script>

<svelte:head>
	<title>The Dad Joke Factory</title>
	<meta name="description" content="Make and rate dad jokes!" />
</svelte:head>
<div
	class="absolute w-full h-full flex justify-center items-center font-display"
	class:hidden={rating == ''}
	onclick={onModalClick}
	bind:this={modal}
>
	<div
		class="w-2/3 h-2/3 flex flex-col justify-center items-center rainbow rounded-[100px] text-5xl text-center p-10 gap-10"
	>
		YOUR JOKE GETS A RATING OF
		<div>{rating}</div>
	</div>
</div>
<div class="flex flex-col items-center font-display p-20 h-full">
	<h1 class="text-5xl pt-40 pb-25">
		The <em class="rainbow-text text-7xl">&nbspDad Joke&nbsp</em>
		Factory
	</h1>
	<div class="w-full h-full flex">
		<div class="w-full p-10 flex flex-col items-center gap-10">
			<em class="text-5xl rainbow-text w-min">Jokes</em>
			{#each jokes as joke}
				<div>{joke}</div>
			{/each}
		</div>
		<div class="w-full p-10 gap-10 flex flex-col items-center">
			<button
				onclick={generateJoke}
				class="rounded-4xl p-5 bg-black text-white h-min transition-opacity w-full"
				disabled={generatingJoke}
				class:opacity-50={generatingJoke}>GENERATE NEW JOKE</button
			>
			<button
				onclick={generateJokeResponse}
				class="rounded-4xl p-5 bg-black text-white h-min transition-opacity w-full"
				disabled={generatingJoke}
				class:opacity-50={generatingJoke}>GENERATE JOKE AS RESPONSE</button
			>
			<button
				onclick={rateJoke}
				class="rounded-4xl p-5 bg-black text-white h-min transition-opacity w-full"
				disabled={generatingJoke}
				class:opacity-50={generatingJoke}>RATE MY JOKE</button
			>
			{#if generatingJoke}
				GENERATING JOKE...
			{/if}
		</div>
	</div>
</div>

<style>
	.rainbow-text {
		background: red;
		background: -webkit-linear-gradient(left, red, orange, yellow, green, blue, indigo, violet);
		background: -o-linear-gradient(right, red, orange, yellow, green, blue, indigo, violet);
		background: -moz-linear-gradient(right, red, orange, yellow, green, blue, indigo, violet);
		background: linear-gradient(to right, red, orange, yellow, green, blue, indigo, violet);
		-webkit-background-clip: text;
		-webkit-text-fill-color: transparent;
	}
	.rainbow {
		background: red; /* For browsers that do not support gradients */
		background: -webkit-linear-gradient(
			left,
			red,
			orange,
			yellow,
			green,
			blue,
			indigo,
			violet
		); /* For Safari 5.1 to 6.0 */
		background: -o-linear-gradient(
			right,
			red,
			orange,
			yellow,
			green,
			blue,
			indigo,
			violet
		); /* For Opera 11.1 to 12.0 */
		background: -moz-linear-gradient(
			right,
			red,
			orange,
			yellow,
			green,
			blue,
			indigo,
			violet
		); /* For Firefox 3.6 to 15 */
		background: linear-gradient(
			to right,
			red,
			orange,
			yellow,
			green,
			blue,
			indigo,
			violet
		); /* Standard syntax (must be last) */
	}
</style>
