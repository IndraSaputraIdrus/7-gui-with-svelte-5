<script lang="ts">
	type Circle = {
		id: string;
		cx: number;
		cy: number;
		r: number;
	};

	type Status = 'drawing' | 'editing';

	let status = $state<Status>('drawing');
	let circles = $state<Circle[]>([]);
	let selected = $state<Circle>()!;

	let snapshots: Circle[][] = [];
	let history = $state(-1);

	function drawCircle(e: MouseEvent) {
		if (status === 'editing') {
			snapshot();
			status = 'drawing';
			return;
		}

		const svgEl = e.target as SVGElement;
		const { left, top } = svgEl.getBoundingClientRect();

		const circle: Circle = {
			id: window.crypto.randomUUID(),
			cx: +(e.clientX - left).toFixed(),
			cy: +(e.clientY - top).toFixed(),
			r: 40
		};

		circles.push(circle);
		selected = circle;
		snapshot();
	}

	function undo() {
		circles = snapshots[--history];
	}

	function redo() {
		circles = snapshots[++history];
	}

	function snapshot() {
		history++;
		snapshots.push($state.snapshot(circles));
	}
</script>

<section class="flex h-full items-center justify-center">
	<div>
		<h1 class="mb-4 text-center text-2xl font-semibold">Cirle Draw</h1>
		<div class="relative space-y-4">
			<div class="items-enter flex justify-center gap-2">
				<button onclick={undo} disabled={history === -1}>Undo</button>
				<button onclick={redo} disabled={history === snapshots.length - 1}>Redo</button>
			</div>

			{#if status === 'editing'}
				<div
					class="absolute bottom-[10%] left-1/2 w-1/2 w-[70%] -translate-x-1/2 rounded border bg-zinc-800 p-3"
				>
					<label class="flex flex-col justify-center space-y-3">
						<span class="text-center text-sm"
							>Adjust diameter of circle at {selected.cx}, {selected.cy}</span
						>
						<input type="range" id="radius" bind:value={selected.r} />
					</label>
				</div>
			{/if}

			<!-- svelte-ignore a11y_click_events_have_key_events -->
			<!-- svelte-ignore a11y_no_static_element_interactions -->
			<svg onclick={drawCircle} class="h-[400px] w-[600px] border" viewBox="0 0 600 400">
				{#each circles as circle}
					<circle
						stroke="#fff"
						stroke-width="2"
						class={`${selected.id === circle.id ? 'fill-gray-500' : 'fill-transparent'} transition`}
						{...circle}
						onclick={(e) => {
							e.stopPropagation();
							selected = circle;
						}}
						oncontextmenu={(e) => {
							if (status === 'editing') {
								snapshot();
							}
							e.preventDefault();
							status = 'editing';
							selected = circle;
						}}
					></circle>
				{/each}
			</svg>
		</div>
	</div>
</section>

<style lang="postcss">
	button {
		@apply block rounded border border-zinc-900 bg-gradient-to-b from-white via-zinc-200 to-zinc-400 px-7 py-0.5 text-center font-semibold text-zinc-900 transition hover:opacity-90 active:scale-105 disabled:opacity-60 disabled:cursor-not-allowed;
	}
</style>
