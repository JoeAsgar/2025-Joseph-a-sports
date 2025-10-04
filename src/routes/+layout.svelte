<script>
	import '../app.css';
	import favicon from '$lib/assets/favicon.svg';

	let { children } = $props();
</script>

<svelte:head>
	<link rel="icon" href={favicon} />
</svelte:head>

<nav class="space-x-5 bg-gray-300 p-2">
	<a href="/">HOME</a>
	<a href="/about">About Us</a>
</nav>

{@render children?.()}
