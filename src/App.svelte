<script>
	import { onMount } from 'svelte';

	export let searchText = "";
	export let entries = [];
	export let possibleValuesForHttps = [];
	export let selectedValueForHttps = null;
	export let possibleValuesForCors = [];
	export let selectedValueForCors = null;
	export let possibleValuesForCategory = [];
	export let selectedValueForCategory = null;
	$: entriesReactive = getEntriesToDisplay(
		entries,
		selectedValueForHttps,
		selectedValueForCors,
		selectedValueForCategory,
		searchText,
	);

	function getEntriesToDisplay(
		entries,
		selectedValueForHttps,
		selectedValueForCors,
		selectedValueForCategory,
		searchText,
	) {
		let result = [...entries];
		if (selectedValueForHttps !== null) {
			result = result.filter(e => e["HTTPS"] === selectedValueForHttps)
		}
		if (selectedValueForCors !== null) {
			result = result.filter(e => e["Cors"] === selectedValueForCors)
		}
		if (selectedValueForCategory !== null) {
			result = result.filter(e => e["Category"] === selectedValueForCategory)
		}
		const textToMatch = searchText.toLowerCase().trim();
		if (!!textToMatch) {
			result = result.filter(e =>
											e.API.toLowerCase().includes(textToMatch) ||
											// e.Description.toLowerCase().includes(textToMatch) ||
											e.Category.toLowerCase().includes(textToMatch)
										);
		}
		return result;
	}

	function reset() {
		selectedValueForHttps = null;
		selectedValueForCors = null;
		selectedValueForCategory = null;
		searchText = "";
	}

	// 	{
	// 		"API": string;
	// 		"Description": string;
	// 		"Auth": string;
	// 		"HTTPS": boolean;
	// 		"Cors": "yes" | "no" | "unknown";
	// 		"Link": string;
	// 		"Category": string;
	// 	}[]
	async function getEntries() {
		const api = "https://api.publicapis.org/entries";
		const response = await fetch(api).then(r => r.json());
		const result = response ? response.entries : [];
		return result;
	}

	async function fetchAndSetEntries() {
		entries = await getEntries();
	}

	function getAllPossibleValuesFor(key, entries) {
		return Array.from(new Set(entries.map(e => e[key])));
	}

	async function initEverything() {
		await fetchAndSetEntries();
		setFilters();
	}

	function setFilters() {
		possibleValuesForHttps = getAllPossibleValuesFor("HTTPS", entries);
		possibleValuesForCors = getAllPossibleValuesFor("Cors", entries);
		possibleValuesForCategory = getAllPossibleValuesFor("Category", entries);
	}

	onMount(initEverything);

</script>

<svelte:head>
	<title>PublicApisBrowser</title>
	<meta name="viewport" content= "width=device-width, initial-scale=1.0"> 
	<link rel="stylesheet" href="https://unpkg.com/sakura.css/css/sakura.css" type="text/css">
	<style>
		html, body {
			height: 100%;
			overflow: auto;
		}
		body {
			padding: 0;
			max-width: 58em;
		}
	</style>
</svelte:head>

<header>
	<div class="search">
		<input bind:value={searchText} placeholder="Search" autofocus>
	</div>

	<div class="filters">
		<div class="filter filter--https">
			<p>HTTPS</p>
			<select bind:value={selectedValueForHttps}>
				<option value={null}>
					All
				</option>
				{#each possibleValuesForHttps as value}
					<option value={value}>
						{value}
					</option>
				{/each}
			</select>
		</div>
		<div class="filter filter--cors">
			<p>Cors</p>
			<select bind:value={selectedValueForCors}>
				<option value={null}>
					All
				</option>
				{#each possibleValuesForCors as value}
					<option value={value}>
						{value}
					</option>
				{/each}
			</select>
		</div>
		<div class="filter filter--category">
			<p>Category</p>
			<select bind:value={selectedValueForCategory}>
				<option value={null}>
					All
				</option>
				{#each possibleValuesForCategory as value}
					<option value={value}>
						{value}
					</option>
				{/each}
			</select>
		</div>
		<div class="filter filter--reset">
			<button on:click={reset}>Reset</button>
		</div>
	</div>
</header>
<main>
	<h1>APIs:</h1>
	<small>(Displaying {entriesReactive.length} out of {entries.length})</small>
	<table>
		<thead>
			<tr>
				<th>#</th>
				<th>API</th>
				<th>HTTPS</th>
				<th>Cors</th>
				<th>Category</th>
			</tr>
		</thead>
		<tbody>
			{#each entriesReactive as { API, Description, Auth, HTTPS, Cors, Link, Category }, i}
				<tr>
					<td>{i+1}</td>
					<td>
						<a href={Link} target="_blank" rel="noopener">{API}</a>
					</td>
					<td>{HTTPS}</td>
					<td>{Cors}</td>
					<td>{Category}</td>
				</tr>
			{/each}
		</tbody>
	</table>
</main>

<style>

header {
	position: sticky;
	top: 0;
	background: rgba(249, 249, 249, 0.97);
	padding: 2rem 0;
	border-bottom: 1px dashed #cacaca;
}

.filter {
    display: inline-block;
    margin-right: 2rem;
}

.search input {
    width: 100%;
}

</style>