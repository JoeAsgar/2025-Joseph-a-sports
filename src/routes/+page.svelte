<script>
	import { onMount } from 'svelte';

	let games = $state([]);
	let players = $state([]);
	let game = $state(undefined);

	// New game registration fields
	let opponent = $state('');
	let gameDate = $state(''); // yyyy-mm-dd
	let ourScore = $state(0);
	let oppScore = $state(0);

	let playerPerformance = $state([]);
	let resultsModalEl;
	// {name: "Game 1", points: 2, assists: 1, turnout: "Win"},
	// {name: "Game 2", points: 1, assists: 3, turnout: "Loss"},
	// {name: "Game 3", points: 2, assists: 2, turnout: "Win"},

	onMount(() => {
		let g = localStorage.getItem('games');
		let p2 = localStorage.getItem('players');
		let p = localStorage.getItem('playerPerformance');
		if (g !== null) {
			games = JSON.parse(g);
		}
		if (p !== null) {
			playerPerformance = JSON.parse(p);
		}
		if (p2 !== null) {
			players = JSON.parse(p2);
		}
		// default date to today
		const today = new Date();
		gameDate = new Date(today.getTime() - today.getTimezoneOffset() * 60000)
			.toISOString()
			.slice(0, 10);
	});

	function resultFromScore(us, them) {
		if (Number(us) > Number(them)) return 'WIN';
		if (Number(us) === Number(them)) return 'Tie';
		return 'Loss';
	}

	function addGame() {
		const id = games.length + 1;
		const res = resultFromScore(ourScore, oppScore);
		const game = {
			id,
			opponent: opponent.trim(),
			date: gameDate,
			ourScore: Number(ourScore) || 0,
			opponentScore: Number(oppScore) || 0,
			score: `${Number(ourScore) || 0}-${Number(oppScore) || 0}`,
			result: res
		};

		if (!game.opponent) return; // require opponent

		games = [...games, game];
		localStorage.setItem('games', JSON.stringify(games));

		// reset fields
		opponent = '';
		ourScore = 0;
		oppScore = 0;
		// keep date default
	}

	function updatePoints(points, game_id, player_id) {
		console.log(points, game_id, player_id);
	}

	function selectGame(id) {
		players.forEach((p) =>
			playerPerformance.find((pp) => pp.game_id === id && pp.player_id === p.id)
				? ''
				: playerPerformance.push({
						player_id: p.id,
						game_id: id,
						points: 0,
						assists: 0
					})
		);

		game = id;
		resultsModalEl?.showModal?.();
	}

	function saveResults() {
		console.log(playerPerformance);
		localStorage.setItem('playerPerformance', JSON.stringify(playerPerformance));
	}
</script>

<div class="container mx-auto mt-10 flex flex-col items-center">
	<div class="my-grid2 mt-3 flex grid gap-0.5 p-0.5">
		<div>Date</div>
		<div>Opponent</div>
		<div>Score</div>
		<div>Result</div>
		<div></div>
		{#each games as v, i}
			<div>
				{v.date ?? ''}
			</div>
			<div>{v.opponent ?? '-'}</div>
			<div>{v.score ?? v.points ?? '-'}</div>
			<div>{v.result ?? v.turnout ?? '-'}</div>
			<div>
				<button onclick={() => selectGame(v.id)} class="btn btn-ghost">Edit Detail</button>
			</div>
		{/each}
	</div>

	<div class="mt-5">
		<button class="btn" onclick={() => my_modal_1.showModal()}>New Game</button>
	</div>

	<dialog bind:this={resultsModalEl} class="modal">
		<div class="modal-box max-w-4xl">
			<h3 class="text-lg font-bold">Enter Results</h3>
			{#if game !== undefined}
				<div class="mt-3 grid grid-cols-4 gap-2">
					<div class="font-medium">Name</div>
					<div class="font-medium">Position</div>
					<div class="font-medium">Points</div>
					<div class="font-medium">Assists</div>
					{#each players as player, i}
						<div>{player.name}</div>
						<div>{player.position}</div>
						<div>
							<input
								oninput={(ev) =>
									(playerPerformance = playerPerformance.map((p) =>
										p.game_id === game && p.player_id === player.id
											? { ...p, points: parseInt(ev.target.value) }
											: p
									))}
								value={playerPerformance.find(
									(p) => p.player_id === player.id && p.game_id === game
								)?.points}
								class="input"
								type="number"
							/>
						</div>
						<div>
							<input
								oninput={(ev) =>
									(playerPerformance = playerPerformance.map((p) =>
										p.game_id === game && p.player_id === player.id
											? { ...p, assists: parseInt(ev.target.value) }
											: p
									))}
								value={playerPerformance.find(
									(p) => p.player_id === player.id && p.game_id === game
								)?.assists}
								class="input"
								type="number"
							/>
						</div>
					{/each}
				</div>
				<div class="mt-4 flex justify-end gap-2">
					<button class="btn btn-primary" onclick={saveResults}>Save Results</button>
				</div>
			{:else}
				<div class="alert bg-base-200">No game selected.</div>
			{/if}
			<div class="modal-action">
				<form method="dialog">
					<button class="btn">Close</button>
				</form>
			</div>
		</div>
	</dialog>

	<dialog id="my_modal_1" class="modal">
		<div class="modal-box">
			<h3 class="text-lg font-bold">New Game</h3>
			<div class="mt-3 grid grid-cols-1 gap-3 md:grid-cols-2">
				<label class="form-control w-full md:col-span-2">
					<div class="label"><span class="label-text">Opponent</span></div>
					<input
						bind:value={opponent}
						class="input-bordered input w-full"
						type="text"
						placeholder="e.g., Tigers"
					/>
				</label>
				<label class="form-control w-full">
					<div class="label"><span class="label-text">Date</span></div>
					<input bind:value={gameDate} class="input-bordered input w-full" type="date" />
				</label>
				<label class="form-control w-full">
					<div class="label"><span class="label-text">Our score</span></div>
					<input bind:value={ourScore} class="input-bordered input w-full" type="number" min="0" />
				</label>
				<label class="form-control w-full">
					<div class="label"><span class="label-text">Opponent score</span></div>
					<input bind:value={oppScore} class="input-bordered input w-full" type="number" min="0" />
				</label>
				<div class="md:col-span-2">
					<div class="badge badge-outline">
						Result: {resultFromScore(ourScore, oppScore)}
					</div>
				</div>
			</div>
			<div class="modal-action">
				<form method="dialog">
					<button onclick={addGame} class="btn btn-success">Add</button>
					<button class="btn">Close</button>
				</form>
			</div>
		</div>
	</dialog>
</div>

<style>
	.my-grid2 {
		grid-template-columns: auto auto auto auto auto;
	}

	.my-grid2 > div:nth-child(-n + 5) {
		background-color: rgba(229, 231, 235);
	}

	.my-grid2 > div {
		background: white;
		display: flex;
		justify-content: center;
		align-items: center;
		padding: 10px;
	}
</style>
