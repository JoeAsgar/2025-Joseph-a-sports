<script>
	import { onMount } from 'svelte';

	let players = $state([]);
	let games = $state([]);
	let name = $state('');
	let position = $state('striker');
	let player = $state(undefined);
	let playerPerformance = $state([]);

	onMount(() => {
		const p = localStorage.getItem('players');
		const g = localStorage.getItem('games');
		const p2 = localStorage.getItem('playerPerformance');

		if (p) players = JSON.parse(p);
		if (g) games = JSON.parse(g);
		if (p2) playerPerformance = JSON.parse(p2);
	});

	function addPlayer() {
		const newPlayer = {
			id: players.length + 1,
			name: name.trim(),
			position
		};

		if (!newPlayer.name) return; // simple guard

		players = [...players, newPlayer];
		localStorage.setItem('players', JSON.stringify(players));
		name = '';
		position = 'striker';
		// auto-select the newly added player
		player = newPlayer;
	}

	const totalPoints = (pl) =>
		playerPerformance.reduce((prev, p) => (p.player_id === pl?.id ? prev + p.points : prev), 0);
	const totalAssists = (pl) =>
		playerPerformance.reduce((prev, p) => (p.player_id === pl?.id ? prev + p.assists : prev), 0);

	function selectPlayer(p) {
		player = p;
	}
</script>

<div class="container mx-auto px-4 py-4">
	<div class="mb-4 flex items-center justify-between">
		<h1 class="text-3xl font-semibold tracking-wide">Players</h1>
		<button class="btn" onclick={() => my_modal_1.showModal()}>Add Player</button>
	</div>

	{#if players.length === 0}
		<div class="alert bg-base-200">
			<span>No players yet. Add your first player to get started.</span>
		</div>
	{/if}

	<div class="mt-4 grid grid-cols-1 gap-4 md:grid-cols-3">
		<!-- Players list -->
		<aside class="md:col-span-1">
			<ul class="menu w-full rounded-box bg-base-200">
				{#each players as p}
					<li>
						<button class:active={player?.id === p.id} onclick={() => selectPlayer(p)}>
							<div class="avatar avatar-placeholder">
								<div class="w-8 rounded-full bg-neutral text-neutral-content">
									<span class="text-xl">{p.name?.[0].toUpperCase()}</span>
								</div>
							</div>
							<div class="flex flex-col leading-tight">
								<span class="font-medium">{p.name}</span>
								<span class="text-xs opacity-70">{p.position}</span>
							</div>
						</button>
					</li>
				{/each}
			</ul>
		</aside>

		<!-- Player details -->
		<section class="md:col-span-2">
			{#if player}
				<div class="card mb-4 bg-base-100 shadow-sm">
					<div class="card-body">
						<h2 class="card-title">{player.name}</h2>
						<div class="stats shadow">
							<div class="stat">
								<div class="stat-title">Position</div>
								<div class="stat-value text-lg">{player.position}</div>
							</div>
							<div class="stat">
								<div class="stat-title">Total Points</div>
								<div class="stat-value">{totalPoints(player)}</div>
							</div>
							<div class="stat">
								<div class="stat-title">Total Assists</div>
								<div class="stat-value">{totalAssists(player)}</div>
							</div>
						</div>
					</div>
				</div>

				<div class="card bg-base-100 shadow-sm">
					<div class="card-body">
						<h3 class="card-title">Game Info</h3>
						{#if playerPerformance.filter((p2) => p2.player_id === player.id).length === 0}
							<div class="alert bg-base-200">No game stats yet for this player.</div>
						{:else}
							<div class="overflow-x-auto">
								<table class="table table-zebra">
									<thead>
										<tr>
											<th>Game</th>
											<th>Date</th>
											<th>Points</th>
											<th>Assists</th>
										</tr>
									</thead>
									<tbody>
										{#each playerPerformance.filter((p2) => p2.player_id === player.id) as p}
											<tr>
												<td>{games.find((g) => g.id === p.game_id).opponent}</td>
												<td>{games.find((g) => g.id === p.game_id).date}</td>
												<td>{p.points}</td>
												<td>{p.assists}</td>
											</tr>
										{/each}
									</tbody>
								</table>
							</div>
						{/if}
					</div>
				</div>
			{:else}
				<div class="hero rounded-box bg-base-200">
					<div class="hero-content text-center">
						<div class="max-w-md">
							<h2 class="text-xl font-medium">Select a player</h2>
							<p class="opacity-70">
								Choose a player from the list to see details and game performance.
							</p>
						</div>
					</div>
				</div>
			{/if}
		</section>
	</div>
</div>

<!-- <div class = "p-4">Total Player Info</div>
<div class = "flex grid p-0.5 my-grid h-50 w-70 bg-blue-200 gap-0.5 ">
    <div>Position</div>
    <div>Points</div>
    <div>Assists</div>
</div>

<div class = "p-4">Game Info</div>
<div class = "flex grid my-grid2 p-0.5 h-50 w-100 bg-yellow-200 gap-0.5">
    <div>Game</div>
    <div>Points</div>
    <div>assists</div>
    <div>turnout</div>
</div> -->

<!-- Open the modal using ID.showModal() method -->
<dialog id="my_modal_1" class="modal">
	<div class="modal-box">
		<h3 class="text-lg font-bold">Add Player</h3>
		<div>
			<div>Name:</div>
			<input class="input" type="text" bind:value={name} />
			<div>Position:</div>
			<select class="select" bind:value={position}>
				<option value="striker">Striker</option>
				<option value="forward">Forward</option>
			</select>
		</div>
		<div class="modal-action">
			<form method="dialog">
				<button onclick={addPlayer} class="btn btn-success">Add</button>
				<button class="btn">Close</button>
			</form>
		</div>
	</div>
</dialog>
