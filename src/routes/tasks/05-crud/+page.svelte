<script lang="ts">
	type Person = {
		name: string;
		surname: string;
	};

	let prefix: string = $state('');

	let person = $state<Person>({ name: '', surname: '' });
	let selected = $state<Person>();
	let database = $state<Person[]>([
		{
			name: 'Emil',
			surname: 'Hans'
		},
		{
			name: 'Mustermann',
			surname: 'Max'
		},
		{
			name: 'Tisch',
			surname: 'Roman'
		}
	]);

	let filtered = $derived(
		database.filter(
			(p) => p.name.toLowerCase().startsWith(prefix) || p.surname.toLowerCase().startsWith(prefix)
		)
	);

	$effect(() => {
		person = {
			name: selected?.name ?? '',
			surname: selected?.surname ?? ''
		};
	});

	$effect(() => {
		if (filtered) {
			clearField();
		}
	});

	function createPerson() {
		if (!person.name && !person.surname) return alert('Please enter name and surname');

		database.push(person);
		clearField();
	}

	function updatePerson() {
		if (!selected) return alert('Please select one person');

		const index = database.indexOf(selected);
		database[index] = { name: person.name, surname: person.surname };
		clearField();
	}
	function deletePerson() {
		if (!selected) return alert('Please select one person');

		database = database.filter((p) => p.name !== selected?.name && p.surname !== selected?.surname);
		clearField();
	}

	function clearField() {
		person = {
			name: '',
			surname: ''
		};
	}
</script>

<section class="flex h-full items-center justify-center">
	<div>
		<h1 class="mb-2 text-2xl font-bold">Crud</h1>

		<div class="rounded bg-zinc-300 p-4 text-zinc-900">
			<div class="flex gap-4">
				<div>
					<label class="flex">
						<span class="flex-1">Filter prefix:</span>
						<input bind:value={prefix} type="text" name="filter" />
					</label>

					<select
						bind:value={selected}
						class="my-3 h-28 w-full overflow-y-auto rounded border border-black bg-white"
						size="5"
					>
						{#each filtered as data}
							<option class="px-2 py-1" value={data}>{data.surname}, {data.name}</option>
						{/each}
					</select>
				</div>
				<div class="my-auto flex flex-col gap-3">
					<label>
						<span class="flex-1">Name:</span>
						<input bind:value={person.name} type="text" name="name" />
					</label>
					<label>
						<span class="flex-1">Surname:</span>
						<input bind:value={person.surname} type="text" name="surname" />
					</label>
				</div>
			</div>
			<div class="flex gap-3">
				<button onclick={createPerson}>Create</button>
				<button onclick={updatePerson}>Update</button>
				<button onclick={deletePerson}>Delete</button>
			</div>
		</div>
	</div>
</section>

<style lang="postcss">
	input {
		@apply w-40 rounded border border-zinc-900 bg-white p-1 shadow;
	}

	label {
		@apply flex items-center gap-3;
	}

	button {
		@apply rounded border border-zinc-900 bg-gradient-to-b from-white via-zinc-200 to-zinc-400 px-7 py-0.5 transition hover:opacity-90 active:scale-105;
	}
</style>
