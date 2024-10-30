<script lang="ts">
	type Options = 'one-way' | 'return';
	let selected = $state<Options>('one-way');
	let startDate = $state(getDate());
	let returnDate = $state(getDate());

	function getDate() {
		const date = new Date();
		const [month, day, year] = date
			.toLocaleDateString('en-US', { year: 'numeric', month: '2-digit', day: '2-digit' })
			.split('/');

		return `${year}-${month}-${day}`;
	}

	function handleSubmit(e: Event) {
		e.preventDefault();
		alert(`You have booked ${selected} on ${startDate}`);
	}

</script>

<section class="flex items-center justify-center h-full">
	<div class="space-y-2">
		<h1 class="text-lg">Book flight</h1>

		<form onsubmit={handleSubmit} class="w-56 space-y-2">
			<select bind:value={selected} class="bg-zinc-800 py-2 px-3 rounded w-full block">
				<option value="one-way">one-way flight</option>
				<option value="return">return flight</option>
			</select>

			<input
				bind:value={startDate}
				class="bg-zinc-800 p-2 px-3 w-full rounded"
				required
				min={getDate()}
				type="date"
			/>
			<input
				bind:value={returnDate}
				required
				min={getDate()}
				class="bg-zinc-800 p-2 px-3 w-full rounded disabled:cursor-not-allowed disabled:opacity-20"
				type="date"
				disabled={selected !== 'return'}
			/>

			<button
				disabled={!startDate || (selected === 'return' && returnDate < startDate)}
				class="bg-indigo-200 text-indigo-700 block w-full p-2 rounded shadow font-semibold disabled:opacity-20 disabled:cursor-not-allowed"
				>Book</button
			>
		</form>
	</div>
</section>
