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

	function drawCircle(e: MouseEvent) {
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
	}
</script>

<section class="flex h-full items-center justify-center">
	<div>
		<h1 class="mb-4 text-center text-2xl font-semibold">Cirle Draw</h1>
		<div class="space-y-4">
			<div class="items-enter flex justify-center gap-2">
				<button>Undo</button>
				<button>Redo</button>
			</div>

			<!-- svelte-ignore a11y_click_events_have_key_events -->
			<!-- svelte-ignore a11y_no_static_element_interactions -->
			<svg onclick={drawCircle} class="h-[400px] w-[600px] border" viewBox="0 0 600 400">
				{#each circles as circle}
					<circle
						stroke="#fff"
						stroke-width="2"
						fill={selected.id === circle.id ? 'gray' : 'transparent'}
						{...circle}
						onclick={(e) => {
              e.stopPropagation()
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
		@apply block rounded border border-zinc-900 bg-gradient-to-b from-white via-zinc-200 to-zinc-400 px-7 py-0.5 text-center font-semibold text-zinc-900 transition hover:opacity-90 active:scale-105;
	}
</style>
