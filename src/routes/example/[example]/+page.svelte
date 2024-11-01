<script lang="ts">
	import { page } from '$app/stores';

	let example = $derived(import(`../../../lib/examples/${$page.params.example}/index.svelte`));
</script>

{#await example then component}
	<component.default />
{/await}
