<script lang="ts">
	const rows = 10;
	const cols = 10;
	const letters = 'ABCDEFGHIJKLMNOPQRSTUVWXYZ';

	const data = $state([
		[{ value: 'Item' }, { value: 'Price' }, { value: 'Amount' }, { value: 'Total' }],
		[{ value: 'Headset' }, { value: '50000' }, { value: '2' }, { value: '=MULTIPLY(B2,C2)' }],
		[{ value: 'Mouse' }, { value: '30000' }, { value: '4' }, { value: '=MULTIPLY(B3,C3)' }],
		[{ value: '' }, { value: '' }, { value: 'Total' }, { value: '=SUM(D2,D3)' }]
	]);

	let selectedCell: string | null = $state(null);
	let editedCell: string | null = $state(null);

	function parseValue(value: string): string | number {
		if (value.startsWith('=')) {
			const { operation, cells } = parseFormula(value);

			const values = cells.map(({ col, row }) => {
				const value = data[row][col].value;
				console.log(value);

				if (value.startsWith('=')) {
					return +parseValue(value);
				}

				return +value;
			});

			return values.reduce(
				(total, value) => {
					if (operation === 'SUM') return total + value;
					if (operation === 'MULTIPLY') return total * value;
					return value;
				},
				operation === 'MULTIPLY' ? 1 : 0
			);
		}

		return value;
	}

	function parseFormula(value: string) {
		// =SUM(B1,B2)
		const [a, b] = value.split('(');
		const operation = a.split('=')[1];
		const cells = b.replace(')', '').split(',');
		return { operation, cells: cells.map(cellNameToIndex) };
	}

	function cellNameToIndex(value: string) {
		// A1 -> 00 -> data[0][0]
		// B1 -> 10 -> data[1][0]
		const [col, ...row] = value.split('');
		return { row: Number(row.join('')) - 1, col: letters.indexOf(col.toUpperCase()) };
	}

	function update(e: Event) {
		const { value, parentElement } = e.target as HTMLInputElement;
		const { row, col } = cellNameToIndex(parentElement!.dataset.cell!);
		if (data[row]) {
			if (data[row][col]) {
				data[row][col].value = value;
			} else {
				data[row][col] = { value };
			}
		} else {
			data[row] = [];
			data[row][col] = { value };
		}
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
					{@const editing = editedCell === cell}
					<td
						onclick={() => {
							if (selected) return;
							selectedCell = cell;
							editedCell = null;
						}}
						ondblclick={() => {
							editedCell = cell;
						}}
						data-cell={cell}
						class={[
							'h-10 w-40 text-left font-semibold',
							'border border-zinc-800 px-2',
							'transition-all duration-100 ease-in-out',
							selected && 'outline outline-indigo-600'
						]}
					>
						{#if editing}
							<!-- svelte-ignore a11y_autofocus -->
							<input
								type="text"
								class="w-full bg-transparent outline-none"
								oninput={update}
								{value}
								autofocus
							/>
						{:else}
							<span>{parsedValue}</span>
						{/if}
					</td>
				{/each}
			</tr>
		{/each}
	</tbody>
</table>
