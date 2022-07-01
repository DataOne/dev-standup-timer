<script>
	export let timerInSeconds;
	$: timeRemainingInSeconds = timerInSeconds;
	$: halfOfTimeInSeconds = timerInSeconds / 2;
	let oldTimerId = -1;

	function startTimer() {
		clearInterval(oldTimerId);
		timeRemainingInSeconds = timerInSeconds;

		oldTimerId = setInterval(() => {
			if (timeRemainingInSeconds === 1) {
				clearInterval(oldTimerId);
				blink();
			}
			timeRemainingInSeconds -= 1;
		}, 1000);
	}

	function blink() {
		document.body.style.backgroundColor = "red";

		setTimeout(() => {
			document.body.style.backgroundColor = "unset";
		}, 500);
	}
</script>

<div class="timer">
	<i>Timer [s]</i> <br />
	<input
		type="text"
		bind:value={timeRemainingInSeconds}
		style="width: 133px; height: 95px; text-align: center; font-size: xxx-large; {timeRemainingInSeconds ===
		0
			? 'color: red;'
			: timeRemainingInSeconds <= halfOfTimeInSeconds
			? 'color: #d7bf22'
			: ''}"
		readonly
	/> <br />
	<button type="button" on:click={startTimer}>⏱ Timer starten</button>
</div>

<style>
	.timer {
		position: fixed;
		z-index: 2;
		right: 10%;
		top: 15px;
		padding: 20px;
		background: #ececec;
	}
</style>
