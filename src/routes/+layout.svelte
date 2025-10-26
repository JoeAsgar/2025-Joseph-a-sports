<script>
	import '../app.css';

	let { children } = $props();
</script>

<nav class="space-x-5 bg-gray-300 p-2">
	<a href="/">HOME</a>
	<a href="/points">Team Stats</a>
	<a href="/Players">Players</a>
</nav>

{@render children?.()}
