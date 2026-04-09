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
        justify-content: center;
    }
    main {
        width: 100%;
        max-width: 800px;
        margin: 0 auto;
        padding: 1rem; 
    }
</style>

