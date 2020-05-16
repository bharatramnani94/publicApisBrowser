<script>
	import { onMount } from 'svelte';

	export let entries = [];

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
		const entries = response ? response.entries : [];
		console.log(`Fetched ${entries.length} entries.`);
		return entries;
	}

	async function fetchAndSetEntries() {
		entries = await getEntries();
		console.log(`Set entries.`);
	}

	onMount(fetchAndSetEntries);

</script>

<main>
	<h1>APIs:</h1>
	<ul>
		{#each entries as { API, Description, Auth, HTTPS, Cors, Link, Category }, i}
			<li>
				{i + 1}: {API}
			</li>
		{/each}
	</ul>

</main>

<style>
	
</style>