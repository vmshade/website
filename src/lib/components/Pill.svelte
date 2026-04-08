<script lang="ts">
	let { text = '' } = $props();
</script>

<div class="pill">
	<slot name="icon" />
	<span>{text}</span>
</div>

<style>
	.pill {
		display: inline-flex;
		align-items: center;
		gap: 0.4em;

		padding: 0.6em 1.1em;
		border-radius: 999px;

		background: #111;
		color: white;

		font-size: 16px;
		line-height: 1;
		margin: 10px 5px 0 0;
	}

	.pill :global(svg) {
		width: 1.1em;
		height: 1.1em;
		display: block;
		flex-shrink: 0;
		fill: currentColor;
	}
</style>
