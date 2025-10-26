<script>
	import { onMount } from "svelte";

	let points = $state([]);
	let players = $state([]);
	let playerPerformance = $state([]);


	onMount(() => {
		let p = localStorage.getItem("players");
		let p2 = localStorage.getItem("playerPerformance");

		if(p !== null) {
			players = JSON.parse(p);
		}
		if(p2 !== null) {
			playerPerformance = JSON.parse(p2);
		}

		console.log(players);
	});


</script>
<div class = "m-4 text-5xl">Team Stats</div>
<div class="flex grid my-grid grid-cols-5 h-100 gap-0.5 bg-green-300 p-0.5">
	<div>#</div>
	<div>Name</div>
	<div>Position</div>
	<div>Points</div>
	<div>Assists</div>
	{#each players as player, i}
		<div>{player.id}</div>
		<div>{player.name}</div>
		<div>{player.position}</div>
		<div>{playerPerformance.reduce((prev, p) => {

			if(p.player_id === player.id) {
				return prev + p.points;
			}
			return prev;
		}, 0)}</div>
		<div>{playerPerformance.reduce((prev, p) => {

			if(p.player_id === player.id) {
				return prev + p.assists;
			}
			return prev;
		}, 0)}</div>
	{/each}

</div>

<style>
	.my-grid {
		grid-template-columns: auto auto auto auto auto;
		
	}

	.my-grid > div:nth-child(-n + 5) {
		background-color: rgba(229, 231, 235);
	}

	.my-grid > div {
		background: white;
		display: flex;
		justify-content: center;
		align-items: center;
		padding: 2px;
		border: 1;
	}
</style>
