<script lang="ts">
	function temperature() {
		let celcius = $state(0);
		let fahrenheit = $state(0);

		return {
			get celcius() {
				return celcius;
			},
			set celcius(newCelcius) {
				celcius = newCelcius;
				fahrenheit = celcius * (9 / 5) + 32;
			},
			get fahrenheit() {
				return fahrenheit;
			},
			set fahrenheit(newFahrenheit) {
				fahrenheit = newFahrenheit;
				celcius = (fahrenheit - 32) * (5 / 9);
			}
		};
	}

	const tempValue = temperature();
</script>

<section class="flex items-center justify-center h-full">
	<div class="space-y-2">
		<h1 class="text-lg">Temperature Converter</h1>

		<div class="flex items-center gap-3">
			<div class="space-x-2">
				<input
					bind:value={tempValue.celcius}
					class="p-2 rounded bg-zinc-800"
					type="number"
					name="celcius"
				/>
				<label for="celcius">Celcius</label>
			</div>
			<p>=</p>
			<div class="space-x-2">
				<input
					bind:value={tempValue.fahrenheit}
					class="p-2 rounded bg-zinc-800"
					type="number"
					name="fahrenheit"
				/>
				<label for="fahrenheit">Fahrenheit</label>
			</div>
		</div>
	</div>
</section>
