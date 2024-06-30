<script>
import { onDestroy } from "svelte";

/*

// TODO: "Rampdown" configuration
// TODO: "Auto-run" settings
// TODO: Tab/Favicon notifications
// TODO: Browser notifications
// TODO: Add Tones
// TODO: Add Tone Config

// TODO: Rest activities?

*/




let presets = [
	// { name: "30 minute pomodoro", work: 1500, rest: 300, isRest: false, isRampdown: false },
	{ name: "30 minute pomodoro", work: 15, rest: 3, isRest: false, isRampdown: false },
	// { name: "60 minute pomodoro", work: 2700, rest: 900, isRest: false, isRampdown: false },
	{ name: "60 minute pomodoro", work: 27, rest: 9, isRest: false, isRampdown: false },
];

let selected = presets[0];
let running = false;

$: workTimer = selected.work;
$: restTimer = selected.rest;

$: isResting = selected.isRest;
$: isRampdown = selected.isRampdown;

function handleReset() {

	running = false;
	workTimer = selected.work;
	restTimer = selected.rest;
	
	isResting = false;
	isRampdown = false;
}

const interval = setInterval(() => {

	if(!running) return;

	if(workTimer > 0 && !isResting) {

		// countdown work
		workTimer -= 1;

		if(workTimer === 0) {
			// switch to rampdown
			isRampdown = true;
			running = false;
		}
	}

	if(restTimer > 0 && isResting) {

		restTimer -= 1;

		if(restTimer === 0) {

			running = false;
		}
	}

}, 1000);

onDestroy(() => clearInterval(interval));
</script>

<select bind:value={selected}>
	{#each presets as preset}
		<option value={preset}>{preset.name}</option>
	{/each}
</select>

<button type="button" disabled={(workTimer === 0 && !isResting) || (restTimer === 0 && isResting)} on:click={() => running = !running}>
	{running ? "Pause" : "Start"}
</button>

{#if isRampdown}
	<button class="button" on:click={() => {
		isRampdown = false;
		isResting = true;
		running = true;
	}}>Start Rest</button>
{/if}

<button class="button" on:click={handleReset}>Reset</button>

<br />
Work Timer: {workTimer} seconds left
<br />
Rest Timer: {restTimer} seconds left
<br />
<br />
In Rampdown: {isRampdown}
<br />
In Rest: {isResting}
<br />

<div class="lights">
	<div class="light" style="background-color: {workTimer > 0 && !isResting ? 'green' : 'gray'}"></div>
	<div class="light" style="background-color: {isRampdown ? 'yellow' : 'gray'}"></div>
	<div class="light" style="background-color: {restTimer > 0 && isResting ? 'red' : 'gray'}"></div>
</div>

{#if running}

	Running

{:else}

{/if}

<style>
.light {
	border: 1px solid black;
	height: 30px;
	width: 30px;
}
</style>