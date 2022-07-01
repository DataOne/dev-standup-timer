<script>
	import Timer from "./Timer.svelte";
	import { onMount } from "svelte";

	let developers = [];
	let devName = "";
	let timerInSeconds = 60;
	const storageKey = "developers";

	onMount(() => setDevsFromLocalStorage());

	function addDev() {
		developers = [...developers, devName];
		devName = "";
		saveDevsInLocalStorage();
	}

	function removeDev(i) {
		return () => {
			developers = [...developers.slice(0, i), ...developers.slice(i + 1)];
			saveDevsInLocalStorage();
		};
	}

	function saveDevsInLocalStorage() {
		localStorage.setItem(storageKey, developers.join(";"));
	}

	function setDevsFromLocalStorage() {
		const devString = localStorage.getItem(storageKey);
		developers = devString?.split(";") ?? [];
	}

	function sortDevs() {
		for (let i = developers.length - 1; i > 0; i--) {
			const j = Math.floor(Math.random() * (i + 1));
			[developers[i], developers[j]] = [developers[j], developers[i]];
		}
	}
</script>

<h1>Developer Stand-Up</h1>

Zeit pro Person:<input
	type="number"
	bind:value={timerInSeconds}
	style="width: 55px;"
/>
Sekunden<br />

<Timer {timerInSeconds} />

<h3>Teilnehmer</h3>
<input
	type="text"
	placeholder="Name"
	bind:value={devName}
	on:keyup={(e) => e.key === 'Enter' && addDev()}
/>
<button type="button" on:click={addDev}>➕ Hinzufügen</button>
<button type="button" on:click={sortDevs}>🔁 Mischen</button>

{#each developers as developer, i}
	<hr />
	<div>
		<button type="button" on:click={removeDev(i)} style="border: none;"
			>❌</button
		>&nbsp;&nbsp;
		<div style="width: 200px; display:inline-block;">{developer}</div>
		<input type="checkbox" />
	</div>
{/each}

<style>
	:global(body) {
		transition: all 250ms;
	}
</style>
