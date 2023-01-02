<script>
	import Timer from "./Timer.svelte";
	import { onMount } from "svelte";

	let developers = [];
	let checked = {};
	let devName = "";
	const storageKey = "developers";

	onMount(() => setDevsFromLocalStorage());

	function addDev() {
		developers = [...developers, devName];
		devName = "";
		saveDevsInLocalStorage();
	}

	function addDevs(devs) {
		developers = [...developers, ...devs];
		devName = "";
		saveDevsInLocalStorage();
	}

	function removeDev(i) {
		return () => {
			developers = [...developers.slice(0, i), ...developers.slice(i + 1)];
			checked = {};
			saveDevsInLocalStorage();
		};
	}

	function saveDevsInLocalStorage() {
		localStorage.setItem(storageKey, developers.join(";"));
	}

	function setDevsFromLocalStorage() {
		const devString = localStorage.getItem(storageKey);
		if (devString) {
			developers = devString.split(";");
		} else {
			developers = [];
		}
	}

	function sortDevs() {
		for (let i = developers.length - 1; i > 0; i--) {
			const j = Math.floor(Math.random() * (i + 1));
			[developers[i], developers[j]] = [developers[j], developers[i]];
		}
		checked = {};
	}

	function keydownNameField(/**@type KeyboardEvent*/ e) {
		if (e.key === "Enter") {
			e.preventDefault();

			if (devName.trim() !== "") {
				addDev();
			}
		}
	}

	function keyupNameField(/**@type KeyboardEvent*/ e) {
		if (e.key == "v" && e.ctrlKey) {
			addDevs(parsePastedInput(devName));
		}
	}

	function parsePastedInput(/**@type string*/ pasted) {
		pasted = pasted.replaceAll("\r", "");
		const lines = pasted.split("\n");
		const partsBeforeTab = lines
			.map((line) => line.split("\t")[0])
			.filter((line) => line !== "");
		const uniqueNames = [...new Set(partsBeforeTab)];
		return uniqueNames;
	}
</script>

<div class="header">
	<h1>Developer Stand-Up</h1>
</div>

<div class="flex mobile-column">
	<div class="column first">
		<h3>Timer</h3>
		<div class="fixed">
			<Timer />
		</div>
	</div>
	<div class="column">
		<h3>Teilnehmer</h3>
		<div class="fixed controls flex mobile-column">
			<textarea
				type="text"
				placeholder="Name"
				rows="1"
				class="flex-1"
				style="margin-right: 2px; resize: none;"
				title="Namen einzeln eingeben oder mehrere Zeilen mit STRG+V einfügen"
				bind:value={devName}
				on:keyup={keyupNameField}
				on:keydown={keydownNameField}
			/>
			<div class="flex-1 flex mobile-column">
				<button type="button" on:click={addDev}>Hinzufügen</button>
				<button type="button" on:click={sortDevs}>Mischen</button>
			</div>
		</div>

		<div class="dev-box-container">
			{#if developers.length === 0}
				<i>Noch niemanden hinzugefügt.</i>
			{/if}
			<div />
			{#each developers as developer, i}
				<div class="dev-box {checked[i] ? 'checked-item' : ''}">
					<div>
						<input
							type="checkbox"
							id="checkbox-{i}"
							class="clickable"
							bind:checked={checked[i]}
						/>
						<label for="checkbox-{i}" class="dev-name clickable"
							>{developer}</label
						>
					</div>
					<button type="button" on:click={removeDev(i)} style="border: none;">
						❌
					</button>
				</div>
			{/each}
		</div>
	</div>
</div>

<style>
	:global(body) {
		transition: all 250ms;
	}

	div.mobile-column {
		flex-direction: column;
	}

	.header {
		background-color: #3c3c3c;
		margin: -20px;
		color: white;
		margin-bottom: 10px;
		margin-top: 0;
		padding: 15px 20px;
	}

	.header > h1 {
		margin: 0;
		padding: 0;
	}

	.flex {
		display: flex;
		flex-direction: row;
		align-items: stretch;
		justify-content: space-between;
	}

	.flex-1 {
		flex: 1;
	}

	.column {
		flex: 1;
	}

	.dev-name {
		display: inline-block;
		padding: 15px 20px;
		font-size: 19px;
	}

	.dev-box-container {
		margin-top: 40px;
		margin-left: 25px;
	}

	.dev-box {
		background-color: #ececec;
		display: flex;
		align-items: center;
		justify-content: space-between;
		margin-bottom: 10px;
		border-radius: 3px;
	}

	.dev-box:last-child {
		margin-bottom: 100px;
	}
</style>
