<script lang="ts">
	let elapsed = $state(0);
	let duration = $state(5);
	let interval: number;

	function start() {
		interval = setInterval(() => {
			if (elapsed > duration) {
				elapsed = duration;
				clearInterval(interval);
			}
			elapsed = Math.min(elapsed + 0.1, duration);
		}, 100);
	}

	function reset() {
		clearInterval(interval);
		elapsed = 0;
		start();
	}

	$effect(() => {
		clearInterval(interval);

		// Directly adjust elapsed to fit within the new duration
		if (elapsed > duration) {
			elapsed = duration;
		}

		if (!duration) return;
		start();
	});

	$inspect({ duration, elapsed });
</script>

<section class="flex h-full items-center justify-center overflow-x-hidden">
	<div class="space-y-6">
		<h1 class="text-2xl font-bold">Timer</h1>

		<div class="w-72 space-y-3">
			<div class="flex items-center gap-2">
				<span>Elapsed Time:</span>
				<div class="h-3 flex-1 rounded border">
					<div style="width: {(elapsed / duration) * 100}%;" class="h-full bg-teal-200"></div>
				</div>
			</div>
			<p>{elapsed.toFixed(1)}s</p>

			<label class="flex items-center gap-2">
				<span>Duration:</span>
				<input class="flex-1" type="range" bind:value={duration} min="1" max="10" />
			</label>

			<button onclick={reset} class="w-full rounded bg-teal-200 p-2 text-teal-700">Reset</button>
		</div>
	</div>
</section>
