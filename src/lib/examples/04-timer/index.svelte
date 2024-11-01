<script lang="ts">
	let elapsed = $state(0);
	let duration = $state(5);
	let interval: number;

	function start() {
		interval = setInterval(() => {
			elapsed += 0.1;
			if (elapsed > duration) {
				elapsed = duration;
				clearInterval(interval);
			}
		}, 100);
	}

	function reset() {
		elapsed = 0;
		start();
	}

	$effect(() => {
		if (!duration) return;
		start();
	});

</script>

<section class="flex items-center justify-center h-full">
	<div class="space-y-6">
		<h1 class="text-2xl font-bold">Timer</h1>

		<div class="space-y-3 w-72">
			<div class="flex items-center gap-2">
				<span>Elapsed Time:</span>
				<div class="h-3 flex-1 border rounded">
					<div style="width: {(elapsed / duration) * 100}%;" class="h-full bg-teal-200"></div>
				</div>
			</div>
			<p>{elapsed.toFixed(1)}s</p>

			<label class="flex items-center gap-2">
				<span>Duration:</span>
				<input class="flex-1" type="range" bind:value={duration} min="1" max="10" />
			</label>

			<button onclick={reset} class="bg-teal-200 text-teal-700 w-full p-2 rounded">Reset</button>
		</div>
	</div>
</section>
