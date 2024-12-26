<script lang="ts">
	const rows = 10;
	const cols = 10;
	const letters = 'ABCDEFGHIJKLMNOPQRSTUVWXYZ';

	const data = $state([
		[{ value: 'Item' }, { value: 'Price' }, { value: 'Amount' }, { value: 'Total' }],
		[{ value: 'Headset' }, { value: '50.000' }, { value: '2' }, { value: '=MULTIPLY(B2,C2)' }],
		[{ value: 'Mouse' }, { value: '30.000' }, { value: '4' }, { value: '=MULTIPLY(B3,C3)' }],
		[{ value: '' }, { value: '' }, { value: 'Total' }, { value: '=SUM(D2,D3)' }]
	]);

	let selectedCell: string | null = $state(null);
	let editedCell: string | null = $state(null);

  function parseValue(value: string): string | number {
    return value
  }
</script>

<table class="max-w-3xl">
	<thead>
		<tr>
			<th class="w-20 bg-zinc-800"></th>
			{#each { length: rows } as _, i}
				<th class="w-20 bg-zinc-800">{letters[i]}</th>
			{/each}
		</tr>
	</thead>
	<tbody>
		{#each { length: rows } as _, i}
			<tr>
				<th class="h-auto w-20 bg-zinc-800">{i + 1}</th>
				{#each { length: cols } as _, ii}
					{@const value = data?.[i]?.[ii]?.value}
					{@const cell = `${letters[ii]}${i + 1}`}
					{@const selected = selectedCell === cell}
          {@const parsedValue = value ? parseValue(value) : null}
					<td
						onclick={() => {
							if (selected) return;
							selectedCell = cell;
							editedCell = null;
						}}
						data-cell={cell}
						class={[
							'h-10 w-40 text-left font-semibold',
							'border border-zinc-800 px-2',
							'transition-all duration-100 ease-in-out',
							selected && 'outline outline-indigo-600'
						]}>{parsedValue}</td
					>
				{/each}
			</tr>
		{/each}
	</tbody>
</table>
