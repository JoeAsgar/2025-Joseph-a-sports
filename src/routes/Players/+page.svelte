<script>
	import { onMount } from "svelte";


    let players = $state([]);
    let games = $state([]);
    let name = $state("");
    let position = $state("striker");
    let player = $state(undefined);
    let playerPerformance = $state([]);

    onMount(() => {
        let p = localStorage.getItem("players");
        let g = localStorage.getItem("games");
        let p2 = localStorage.getItem("playerPerformance");

        if(p !== null) {
            players = JSON.parse(p);
        }
        if(g !== null) {
            games = JSON.parse(g);
        }
        if(p2 !== null) {
            playerPerformance = JSON.parse(p2);
        }

        console.log(playerPerformance);
    })


    function addPlayer() {
        let player = {
            id: players.length + 1,
            name,
            position,
        }

        players = [...players, player];
        localStorage.setItem("players", JSON.stringify(players));
    }
</script>


<div class = "flex flex-col items-center" >
    <div class = "m-3 p-4 flex justify-center text-3xl">Players</div>

    <select class="select" bind:value={player}>
    <option value={undefined} disabled selected>Pick a player</option>
        {#each players as p, i}
            <option value={p}>{p.name}</option>
        {/each}
    </select>

    <button class="btn" onclick={() => my_modal_1.showModal()}>Add Player</button>




    <div class = "p-4">Total Player Info</div>
    <div class = "flex grid p-0.5 my-grid h-50 w-70 bg-blue-200 gap-0.5 ">
        <div>Position</div>
        <div>Points</div>
        <div>Assists</div>
        <div>{player?.position}</div>
        <div>{playerPerformance.reduce((prev, p) => {

            if(p.player_id === player?.id) {
                return prev + p.points;
            }
            return prev;
        }, 0)}</div>
        <div>{playerPerformance.reduce((prev, p) => {

            if(p.player_id === player?.id) {
                return prev + p.assists;
            }
            return prev;
        }, 0)}</div>

    </div>

    <div class = "p-4">Game Info</div>
    <div class = "flex grid my-grid2 p-0.5 h-50 w-100 bg-yellow-200 gap-0.5">
        <div>Game</div>
        <div>Points</div>
        <div>assists</div>
        {#each playerPerformance.filter((p2) => p2.player_id === player?.id) as p, i}
            <div>{p.game_id}</div>
            <div>{p.points}</div>
            <div>{p.assists}</div>
        {/each}
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
<button class="btn" >open modal</button>
<dialog id="my_modal_1" class="modal">
  <div class="modal-box">
    <h3 class="text-lg font-bold">Add Player</h3>
    <div>
        <div>Name: </div>
        <input class="input" type="text" bind:value={name}/>
        <div>Position: </div>
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
<style>
    .my-grid{
        grid-template-columns: auto auto auto;
    }
    .my-grid2{
        grid-template-columns: auto auto auto;
    }
    .my-grid > div{
        background: white;
		display: flex;
		justify-content: center;
		align-items: center;
		padding: 2px;
    }
    .my-grid2 > div{
        background: white;
		display: flex;
		justify-content: center;
		align-items: center;
		padding: 2px;
    }


</style>