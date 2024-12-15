<script lang="ts">
	import clsx from 'clsx';
	import { fade } from 'svelte/transition';

	type Circle = {
		id: string;
		cx: number;
		cy: number;
		r: number;
	};

	type Status = 'editing' | 'drawing';

	let circles: Circle[] = $state([]);
	let status: Status = $state('drawing');
	let selected: Circle = $state()!;

	let snapshots: Circle[][] = $state([]);
	let history = $state(-1);

	function drawCircle(e: MouseEvent) {
		if (status === 'editing') {
			snapshot();
			status = 'drawing';
			return;
		}

		const element = e.target as SVGElement;
		const { left, top } = element.getBoundingClientRect();

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
		if (history > -1) {
			circles = snapshots[--history];
		}
	}
	function redo() {
		if (history < snapshots.length - 1) {
			circles = snapshots[++history];
		}
	}

	function snapshot() {
		if (history < snapshots.length - 1) {
			snapshots = snapshots.slice(0, history + 1);
		}
		snapshots.push($state.snapshot(circles));
		history++;
	}

	$effect(() => {
		if (circles === undefined) {
			circles = [];
		}
	});
</script>

<section class="grid h-full place-content-center">
	<div class="grid w-[600px] grid-cols-2 gap-5">
		<div class="col-span-2">
			<h1 class="text-center text-3xl font-semibold">Circle draw</h1>
		</div>

		{@render button({ text: 'Undo', onclick: undo, disabled: history === -1 })}
		{@render button({ text: 'Redo', onclick: redo, disabled: history === snapshots.length - 1 })}

		<div class="relative col-span-2 rounded border border-zinc-500">
			<!-- svelte-ignore a11y_click_events_have_key_events -->
			<!-- svelte-ignore a11y_no_static_element_interactions -->
			<svg onclick={drawCircle} viewBox="0 0 600 400" class="h-[400px] w-[600px]">
				{#each circles as circle}
					{@const active = selected.id === circle.id}
					<circle
						{...circle}
						class={clsx(active ? 'fill-zinc-100' : 'fill-transparent')}
						stroke="#fff"
						stroke-width="2"
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

			{#if status === 'editing'}
				<div
					transition:fade={{ duration: 250 }}
					class="absolute bottom-5 left-1/2 flex -translate-x-1/2 flex-col gap-2 rounded bg-zinc-800 p-4"
				>
					<p>Adjust diameter of circle at {selected.cx}, {selected.cy}</p>
					<input
						type="range"
						min="10"
						max="100"
						class="focus:outline-none"
						bind:value={selected.r}
					/>
				</div>
			{/if}
		</div>
	</div>
</section>

{#snippet button({
	text,
	onclick,
	disabled
}: {
	text: string;
	onclick: () => void;
	disabled: boolean;
})}
	<button
		{onclick}
		{disabled}
		class={clsx(
			'rounded bg-white text-black',
			'px-3 py-2',
			'transition-all ease-in-out',
			'hover:bg-white/80 active:scale-105 disabled:bg-white/50'
		)}>{text}</button
	>
{/snippet}
