<script>
	import { onMount } from 'svelte';

	export let entries = [];
	export let entriesDisplayed = [];
	export let possibleValuesForHttps = [];
	export let selectedValueForHttps = null;
	export let possibleValuesForCors = [];
	export let selectedValueForCors = null;

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
		console.log(`Fetching entries.`);
		const api = "https://api.publicapis.org/entries";
		const response = await fetch(api).then(r => r.json());
		const result = response ? response.entries : [];
		console.log(`Fetched ${result.length} entries.`);
		return result;
	}

	async function fetchAndSetEntries() {
		entries = await getEntries();
		entriesDisplayed = [...entries];
		console.log(`Set entries.`);
	}

	function getAllPossibleValuesFor(key, entries) {
		return Array.from(new Set(entries.map(e => e[key])));
	}

	async function initEverything() {
		await fetchAndSetEntries();
		setFilters();
	}

	function setFilters() {
		console.log(`Set possibleValuesForHttps to ${possibleValuesForHttps}`)
		possibleValuesForHttps = getAllPossibleValuesFor("HTTPS", entries);
		possibleValuesForCors = getAllPossibleValuesFor("Cors", entries);
	}

	function handlFilterChangeForHttps() {
		if (selectedValueForHttps === null) {
			entriesDisplayed = [...entries];
		} else {
			entriesDisplayed = entries.filter(e => e["HTTPS"] === selectedValueForHttps);
		}
	}
	
	function handlFilterChangeForCors() {
		if (selectedValueForCors === null) {
			entriesDisplayed = [...entries];
		} else {
			entriesDisplayed = entries.filter(e => e["Cors"] === selectedValueForCors);
		}
	}

	onMount(initEverything);

</script>

<svelte:head>
	<link rel="stylesheet" href="https://unpkg.com/sakura.css/css/sakura.css" type="text/css">
</svelte:head>

<main>

	<div class="filters">
		<div class="filter filter--https">
			<p>HTTPS</p>
			<select bind:value={selectedValueForHttps} on:change={handlFilterChangeForHttps}>
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
		<div class="filter filter--https">
			<p>Cors</p>
			<select bind:value={selectedValueForCors} on:change={handlFilterChangeForCors}>
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
	</div>


	<h1>APIs:</h1>
	<table>
		<thead>
			<tr>
				<th>#</th>
				<th>API</th>
				<th>HTTPS</th>
				<th>Cors</th>
			</tr>
		</thead>
		{#each entriesDisplayed as { API, Description, Auth, HTTPS, Cors, Link, Category }, i}
			<tr>
				<td>{i+1}</td>
				<td>{API}</td>
				<td>{HTTPS}</td>
				<td>{Cors}</td>
			</tr>
		{/each}
	</table>


</main>

<style>
	
</style>