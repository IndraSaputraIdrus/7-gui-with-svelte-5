<script>
	import '../app.css';
	import { tasks } from '$lib/tasks';

	let { children } = $props();
</script>

<main class="flex h-full gap-2 bg-zinc-900 text-zinc-100">
	<aside class="w-56 border-r border-zinc-500 p-4">
		<h2>
			<a class="mb-5 block text-2xl font-semibold" href="/"> 7 Gui tasks </a>
		</h2>
		<ul class="space-y-2">
			{#each tasks as task}
				<li>
					<a class="hover:text-indigo-500" href="/tasks/{task.href}">{task.name}</a>
				</li>
			{/each}
		</ul>
	</aside>

	<div class="h-full flex-1 p-5">
		{@render children()}
	</div>
</main>
