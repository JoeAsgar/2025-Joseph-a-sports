<script>
	import '../app.css';
	import { page } from '$app/stores';

	let { children } = $props();

	// Minimal helper to style the active link based on the current path
	const isActive = (path) => {
		const p = $page.url.pathname;
		if (path === '/') return p === '/';
		return p.startsWith(path);
	};
</script>

<nav class="navbar sticky top-0 z-30 bg-base-200 shadow-sm" aria-label="Primary">
	<div class="container mx-auto">
		<div class="flex-1">
			<a href="/" class="btn text-xl tracking-wide btn-ghost"> SCORIDEX </a>
		</div>
		<div class="flex-none">
			<ul class="menu menu-horizontal gap-1 px-1">
				<li>
					<a href="/" class:active={isActive('/')}>Games</a>
				</li>
				<li>
					<a href="/points" class:active={isActive('/points')}>Stats</a>
				</li>
				<li>
					<a href="/Players" class:active={isActive('/Players')}>Players</a>
				</li>
			</ul>
		</div>
	</div>
</nav>

{@render children?.()}
