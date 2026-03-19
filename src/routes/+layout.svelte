<script lang="ts">
	import '../app.css';
	import favicon from '$lib/assets/vmshade.png';

	let { children } = $props();
</script>

<svelte:head>
	<link rel="icon" href={favicon} />
</svelte:head>

<div class="page">
	<main>
		{@render children()}
	</main>
</div>

<style>
	.page {
		min-height: 100vh;
		display: flex;
		flex-direction: column;
		align-items: stretch;
	}

	main {
		width: 100%;
		max-width: 600px;
		margin: 90px auto 0;
		padding: 0 1rem;
		display: flex;
		flex-direction: column;
		align-items: flex-start;
	}
</style>
