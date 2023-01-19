<script>
	import { onMount, tick } from 'svelte';
	import { fade } from 'svelte/transition';

	let donations = [];

	onMount(async () => {
		const response = await fetch(
			'https://piquetdestream-api.fly.dev/v1/counter/donations?limit=50'
		);
		donations = await response.json();
		const evtSource = new EventSource('https://piquetdestream-api.fly.dev/v1/counter/sse');
		evtSource.addEventListener('new-donation', handleEvent);
		window.ping = handleEvent;
	});

	function handleEvent(event) {
		const newDonation = JSON.parse(event.data);
		donations = [newDonation, ...donations];
	}

	async function getMoreDonations() {
		const response = await fetch(
			`https://piquetdestream-api.fly.dev/v1/counter/donations?limit=50&offset=${donations.length}`
		);
		const newDonations = await response.json();
		donations = [...donations, ...newDonations];
	}
</script>

<table>
	<tr>
		<th>Created at</th>
		<th>Name</th>
		<th>Amount</th>
		<th>Message</th>
	</tr>
	{#each donations as donation}
		<tr class="donation">
			<td
				>{new Date(donation.createdAt).toLocaleDateString().replace('/2023', '')} - {new Date(
					donation.createdAt
				).toLocaleTimeString()}</td
			>
			<td>{donation.name}</td>
			<td>{donation.amount}</td>
			<td class="message">{donation.message}</td>
		</tr>
	{/each}
</table>

<button on:click={getMoreDonations}>Voir plus</button>

<style>
	table {
		border-collapse: collapse;
		font-family: 'Trebuchet MS', 'Lucida Sans Unicode', 'Lucida Grande', 'Lucida Sans', Arial,
			sans-serif;
	}

	th,
	td {
		border: 1px solid #9a0022;
		padding: 10px;
	}

	.message {
		max-width: 300px;
		word-break: break-word;
	}
</style>
