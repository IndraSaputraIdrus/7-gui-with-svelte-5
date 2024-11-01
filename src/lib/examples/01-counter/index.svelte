<script lang="ts">
	let count = $state(0);
</script>

<section class="flex items-center justify-center h-full">
	<div class="space-y-2">
		<h1 class="text-lg">Counter</h1>

		<div class="space-x-2">
			<input class="p-2 rounded" bind:value={count} type="number" name="counter" disabled />
			<button class="bg-indigo-200 text-indigo-700 py-2 px-3 rounded font-semibold transition ease-in-out hover:opacity-80 active:scale-105" onclick={() => count++}>Count</button>
		</div>
	</div>
</section>
