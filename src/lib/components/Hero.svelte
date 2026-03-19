<script>
	import { onMount } from 'svelte';
	let ageElement = $state();

	onMount(() => {
		const birthday = new Date('2011-06-17');
		const msInYear = 365.25 * 24 * 60 * 60 * 1000;
		let displayedAge = (Date.now() - birthday.getTime()) / msInYear;

		function updateAgeSmooth() {
			const age = (Date.now() - birthday.getTime()) / msInYear;
			displayedAge += (age - displayedAge) * 0.1;
			ageElement.textContent = displayedAge.toFixed(3);
			requestAnimationFrame(updateAgeSmooth);
		}
		updateAgeSmooth();
	});
</script>

<p>
	A <span bind:this={ageElement}></span> year old into designing and making minimalistic software, gaming, pixel art,
	watching anime, and hardware development
</p>

<style>
	p {
		font-size: 16px;
		color: var(--text-color);
		margin-top: 10px;
		margin-bottom: 10px;
	}
</style>
