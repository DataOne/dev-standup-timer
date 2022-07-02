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
</script>

<h1>Developer Stand-Up</h1>

<div class="flex">
	<div class="column first">
		<h3>Timer</h3>
		<div class="fixed">
			<Timer />
		</div>
	</div>
	<div class="column">
		<h3>Teilnehmer</h3>
		<div class="fixed controls flex">
			<input
				type="text"
				placeholder="Name"
				bind:value={devName}
				on:keyup={(e) => e.key === "Enter" && addDev()}
			/>
			<div>
				<button type="button" on:click={addDev}>Hinzufügen</button>
				<button type="button" on:click={sortDevs}>Mischen</button>
			</div>
		</div>

		<div class="dev-box-container">
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

	.fixed {
		position: sticky;
		top: 0;
		width: 100%;
		background-color: #fffffff0;
		z-index: 1;
	}

	.flex {
		display: flex;
		flex-direction: row;
		align-items: stretch;
		justify-content: space-between;
	}

	.column {
		flex: 1;
	}

	.column.first {
		border-right: 1px solid #ececec;
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
</style>
