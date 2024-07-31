<script>
	import TypingTest from './Components/TypingTest.svelte';
</script>

<main>
<TypingTest></TypingTest>
</main>

<style>
	main {
		text-align: center;
		padding: 1em;
		max-width: max-content;
		margin: 0 auto;
	}

	h1 {
		color: #ff3e00;
		text-transform: uppercase;
		font-size: 4em;
		font-weight: 100;
	}

	@media (min-width: 640px) {
		main {
			max-width: none;
		}
	}
</style>