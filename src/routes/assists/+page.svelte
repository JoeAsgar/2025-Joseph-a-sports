<script>
	let assists = [
		{ name: 'C', ast: 14, chg: "=0"},
		{ name: 'A', ast: 10, chg: "-1" },
		{ name: 'B', ast: 9,  chg: "-1" },
		{ name: 'D', ast: 11, chg: "+2" }
	];

	assists.sort((b, a) => a.ast - b.ast);
</script>

<div class = "flex flex-col items-center">
	<div class = "m-4 text-5xl"> Assists</div>
	<div class="my-grid inline-grid h-100 w-60 gap-0.5 bg-blue-300 p-0.5">
		<div>#</div>
		<div>Name</div>
		<div>Assists</div>
		<div>Change</div>
		{#each assists as v, i}
			<div>{i + 1}</div>
			<div>{v.name}</div>
			<div>{v.ast}</div>
			<div>{v.chg}</div>
		{/each}
	</div>
</div>

<style>
	.my-grid {
		grid-template-columns: auto auto auto auto;
	}

	.my-grid > div:nth-child(-n + 4) {
		background-color: rgba(229, 231, 235);
	}

	.my-grid > div {
		background: white;
		display: flex;
		justify-content: center;
		align-items: center;
		padding: 2px;
	}
</style>
