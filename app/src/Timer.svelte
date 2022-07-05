<script>
	let timerInSeconds = 60;
	$: timeRemainingInSeconds = timerInSeconds;
	$: halfOfTimeInSeconds = timerInSeconds / 2;
	let oldTimerId = -1;

	function startTimer() {
		stopTimer();
		timeRemainingInSeconds = timerInSeconds;

		oldTimerId = setInterval(() => {
			if (timeRemainingInSeconds === 1) {
				stopTimer();
				blink();
			}
			timeRemainingInSeconds -= 1;
		}, 1000);
	}

	function stopTimer() {
		clearInterval(oldTimerId);
	}

	function blink() {
		document.body.style.backgroundColor = "#E1325A";

		setTimeout(() => {
			document.body.style.backgroundColor = "unset";
		}, 500);
	}
</script>

<div class="controls">
	Zeit pro Person: <input
		type="number"
		bind:value={timerInSeconds}
		style="width: 55px;"
	/>
	Sekunden
</div>

<div class="timer-container">
	<div class="timer">
		<input
			type="text"
			bind:value={timeRemainingInSeconds}
			class="time"
			style={timeRemainingInSeconds === 0
				? "color: #E1325A;"
				: timeRemainingInSeconds <= halfOfTimeInSeconds
				? "color: #d7bf22"
				: ""}
			readonly
		/>
		<button type="button" on:click={startTimer}>Timer starten</button>
	</div>
</div>

<style>
	.timer-container {
		margin-top: 40px;
		margin-bottom: 25px;
		margin-right: 0;
		padding: 20px;
		height: 0;
		padding-bottom: 50%;
		position: relative;
	}

	.timer {
		position: absolute;
		width: 100%;
		height: 100%;
		top: 0;
		left: 0;
	}

	.time {
		width: 100%;
		height: 100%;
		text-align: center;
		font-size: 18vw;
		font-family: "DejaVu Sans Mono", "Cascadia Mono", monospace;
		padding: 0;
	}
</style>
