<script>
	import { onMount } from "svelte";

    let games = $state([]);
    let players = $state([]);
    let game = $state(undefined);
    let name = $state("");
    let points = $state(0);
    let turnout = $state("Win");
    
    let playerPerformance = $state([]);
        // {name: "Game 1", points: 2, assists: 1, turnout: "Win"},
        // {name: "Game 2", points: 1, assists: 3, turnout: "Loss"},
        // {name: "Game 3", points: 2, assists: 2, turnout: "Win"},


    onMount(() => {
        let g = localStorage.getItem("games");
        let p2 = localStorage.getItem("players");
        let p = localStorage.getItem("playerPerformance");
        if(g !== null) {
            games = JSON.parse(g);
        }
        if(p !== null) {
            playerPerformance = JSON.parse(p);
        }
        if(p2 !== null) {
            players = JSON.parse(p2);
        }
    })

    function addGame() {
        let game = {
            id: games.length + 1,
            name,
            points,
            turnout
        }

        games = [...games, game];
        localStorage.setItem("games", JSON.stringify(games));
    }

    function updatePoints(points, game_id, player_id) {
        console.log(points, game_id, player_id);
    }

    function selectGame(id) {
        players.forEach((p) => playerPerformance.find(pp => pp.game_id === id && pp.player_id === p.id) ? "" : playerPerformance.push({
            player_id: p.id,
            game_id: id,
            points: 0,
            assists: 0
        }));

        game = id;
    }

    function saveResults() {
        console.log(playerPerformance);
        localStorage.setItem("playerPerformance", JSON.stringify(playerPerformance));
    }
</script>

<div class = "flex flex-col items-center">
    <button class="btn" onclick={() => my_modal_1.showModal()}>New Game</button>
    

    <div class = "flex grid p-0.5 w-100 bg-yellow-200 gap-0.5 my-grid2">
        <div>Game</div>
        <div>Points</div>
        <div>Turnout</div>
        {#each games as v, i}
            <div><button onclick={() => selectGame(v.id)} class="btn btn-ghost">{v.name}</button></div>
            <div>{v.points}</div>
            <div>{v.turnout}</div>
        {/each}
    </div>
    <div>
        {#if game !== undefined}
            <div class="grid grid-cols-4 mb-10">
                <div>Name</div>
                <div>Position</div>
                <div>Points</div>
                <div>Assists</div>
                {#each players as player, i}
                    <div>{player.name}</div>
                    <div>{player.position}</div>
                    <div><input oninput={ev => playerPerformance = playerPerformance.map((p) => p.game_id === game && p.player_id === player.id ? {...p, points: parseInt(ev.target.value)} : p)} value={playerPerformance.find((p) => p.player_id === player.id && p.game_id === game)?.points} class="input" type="number"/></div>
                    <div><input oninput={ev => playerPerformance = playerPerformance.map((p) => p.game_id === game && p.player_id === player.id ? {...p, assists: parseInt(ev.target.value)} : p)} value={playerPerformance.find((p) => p.player_id === player.id && p.game_id === game)?.assists} class="input" type="number"/></div>
                {/each}
            </div>
            <button class="btn" onclick={saveResults}>Save Results</button>
        {/if}
    </div>

    <dialog id="my_modal_1" class="modal">
    <div class="modal-box">
        <h3 class="text-lg font-bold">Hello!</h3>
        <div>
            <div>
                Name: <input bind:value={name} class="input"  type="text"/>
            </div>
            <div>
                Points: <input bind:value={points} class="input" type="number"/>
            </div>
            <div>
                Turnout: 
                <select class="select" bind:value={turnout}>
                    <option value={"Win"}>Win</option>
                    <option value={"Loss"}>Loss</option>
                </select>
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


<!-- <button class="btn" onclick={() => my_modal_1.showModal()}>New Game</button>
  

<div class = "flex grid grid-cols-3 p-0.5 h-50 w-100 bg-yellow-200 gap-0.5 my-grid2">
    <div>Game</div>
    <div>Points</div>
    <div>Turnout</div>
    {#each games as v, i}
		<div><button onclick={() => selectGame(v.id)} class="btn btn-ghost">{v.name}</button></div>
		<div>{v.points}</div>
        <div>{v.turnout}</div>
	{/each}
</div>
<div>
    {#if game !== undefined}
        <div class="grid grid-cols-4 mb-10">
            <div>Name</div>
            <div>Position</div>
            <div>Points</div>
            <div>Assists</div>
            {#each players as player, i}
                <div>{player.name}</div>
                <div>{player.position}</div>
                <div><input oninput={ev => playerPerformance = playerPerformance.map((p) => p.game_id === game && p.player_id === player.id ? {...p, points: parseInt(ev.target.value)} : p)} value={playerPerformance.find((p) => p.player_id === player.id && p.game_id === game)?.points} class="input" type="number"/></div>
                <div><input oninput={ev => playerPerformance = playerPerformance.map((p) => p.game_id === game && p.player_id === player.id ? {...p, assists: parseInt(ev.target.value)} : p)} value={playerPerformance.find((p) => p.player_id === player.id && p.game_id === game)?.assists} class="input" type="number"/></div>
            {/each}
        </div>
        <button class="btn" onclick={saveResults}>Save Results</button>
    {/if}
</div>

<dialog id="my_modal_1" class="modal">
  <div class="modal-box">
    <h3 class="text-lg font-bold">Hello!</h3>
    <div>
        <div>
            Name: <input bind:value={name} class="input"  type="text"/>
        </div>
        <div>
            Points: <input bind:value={points} class="input" type="number"/>
        </div>
        <div>
            Turnout: 
            <select class="select" value={turnout}>
                <option value={"Win"}>Win</option>
                <option value={"Loss"}>Loss</option>
            </select>
        </div>
    </div>
    <div class="modal-action">
      <form method="dialog">
        <button onclick={addGame} class="btn btn-success">Add</button>
        <button class="btn">Close</button>
      </form>
    </div>
  </div>
</dialog> -->



<style>
    .my-grid2{
        grid-template-columns: auto auto auto ;
        
    }
    .my-grid2 > div{
        background: white;
		display: flex;
		justify-content: center;
		align-items: center;
		padding: 2px;
    }


</style>
