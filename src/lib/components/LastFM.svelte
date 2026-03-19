<script lang="ts">
	import { onMount } from 'svelte';

	let lastTrack = $state('Loading...');
	let error = $state<string | null>(null);

	onMount(async () => {
		try {
			console.log('Fetching from lastfm endpoint...');
			const res = await fetch('https://lastfm.suhayb10910.workers.dev');
			console.log('Response received:', res);
			if (!res.ok) {
				throw new Error(`HTTP error! status: ${res.status}`);
			}
			const data = await res.json();
			console.log('Data received:', data);
			lastTrack = data.track || 'No recent track';
		} catch (e) {
			error = `Failed to fetch last track: ${e instanceof Error ? e.message : String(e)}`;
			console.error('Fetch error:', e);
		}
	});
</script>

<div class="track">
	<p>
		{#if error}
			{error}
		{:else}
			{lastTrack}
		{/if}
	</p>
</div>

<style>
	.track {
		display: flex;
		justify-content: center;
		text-align: center;
        margin-top: 0px;
        margin-bottom: 0px;
	}
	.track p {
		font-size: 16px;
		color: var(--text-color);
        margin-top: 0px;
        margin-bottom: 10px;
	}
</style>
