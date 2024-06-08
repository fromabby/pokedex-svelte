<script lang="ts">
	$: url = 'https://pokeapi.co/api/v2/pokemon'
	$: offset = 0
	$: limit = 25

	const fetchPokemons = async (url: string, offset: number, limit: number) => {
		const response = await fetch(`${url}?offset=${offset}&limit=${limit}`)
		return await response.json()
	}

	const getOffset = (v: string) => {
		if (!v) return 1

		const searchParams = new URLSearchParams(new URL(v).searchParams)
		return parseInt(searchParams.get('offset') ?? '0') + limit + 1
	}

	const getIDFromUrl = (v: string) => {
		return v.split('https://pokeapi.co/api/v2/pokemon/')[1].split('/')[0]
	}

	let selectedPokemons: string[] = []
	const selectPokemon = (name: string) => {
		if (selectedPokemons.includes(name)) {
			// unselect
			selectedPokemons = selectedPokemons.filter((item) => item !== name)
			selectedPokemons = selectedPokemons
			return
		}
		selectedPokemons.push(name)
		selectedPokemons = selectedPokemons
	}
</script>

<main>
	<h1 style="display:inline-block;">Pokedex</h1>
	{#if selectedPokemons.length > 0}
		<p style="display:inline-block;">
			Selected: <strong>{selectedPokemons.length}</strong> pokemons
		</p>
	{/if}
	{#await fetchPokemons(url, offset, limit)}
		<p>...waiting</p>
	{:then data}
		<section class="pokedex">
			{#each data.results as pokemon (pokemon.name)}
				<!-- svelte-ignore a11y-no-static-element-interactions -->
				<!-- svelte-ignore a11y-click-events-have-key-events -->
				<div
					on:click={() => selectPokemon(pokemon.name)}
					class={selectedPokemons.includes(pokemon.name) ? 'selected' : ''}
				>
					<img
						src={'https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/' +
							getIDFromUrl(pokemon.url) +
							'.png'}
						alt={pokemon.name}
					/>
					<p>{pokemon.name}</p>
				</div>
			{/each}
		</section>
		<div class="pagination">
			<div class="pagination-btn">
				<button
					on:click={() => {
						if (data.previous) {
							url = data.previous
						}
					}}>Prev</button
				>
				<button
					on:click={() => {
						if (data.next) {
							url = data.next
						}
					}}>Next</button
				>
			</div>
			<div class="pagination-select">
				<label for="limit">Items:</label>
				<select bind:value={limit}>
					<option value={25}>25</option>
					<option value={50}>50</option>
					<option value={75}>75</option>
					<option value={100}>100</option>
				</select>
			</div>
			<p>
				<strong>{getOffset(data.previous)}</strong>-<strong
					>{getOffset(data.previous) + limit - 1}</strong
				>
				of {data.count}
				pokemons
			</p>
		</div>
	{:catch error}
		<p>An error occurred!</p>
	{/await}
</main>

<style>
	main {
		padding: 1rem;
	}

	.pokedex {
		display: grid;
		gap: 1rem;
		margin-top: 1rem;
		grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
		user-select: none;
	}

	.pokedex > div {
		border-radius: 7px;
		border: 4px solid black;
		padding: 1rem;
		box-shadow: 0px 0px 18px -3px rgba(0, 0, 0, 0.1);
		display: grid;
		grid-template-rows: subgrid;
		grid-row: span 2;
		cursor: pointer;
	}

	.pokedex > div > img {
		width: 100px;
		height: 100px;
		margin: auto;
	}

	.pokedex > div > p {
		font-size: 1.25rem;
		text-align: center;
		padding: 0.25rem 1rem;
	}

	button {
		padding: 0.5rem 1rem;
		border-radius: 7px;
		border: 2px solid #ccc;
		font-weight: 600;
		margin: 0.5rem;
	}

	select {
		width: 100px;
		padding: 0.25rem;
	}

	.pagination {
		display: flex;
		width: 100%;
		padding: 1rem;
		gap: 1rem;
		justify-content: center;
		align-items: center;
	}

	.selected {
		background-color: rgba(0, 0, 0, 0.1);
		transform: scale(1.05);
		transition: transform 0.1s ease;
	}
</style>
