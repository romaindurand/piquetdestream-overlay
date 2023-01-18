<script>
	import { onMount } from 'svelte';

	let counter = 0;

	onMount(async () => {
		const response = await window.fetch('https://piquetdestream-api.fly.dev/v1/counter/state');
		const data = await response.json();
		counter = data.amount;
		const evtSource = new EventSource('https://piquetdestream-api.fly.dev/v1/counter/sse');
		evtSource.addEventListener('counter-update', handleEvent);
	});

	function handleEvent(event) {
		const data = JSON.parse(event.data);
		counter = data.amount;
	}
</script>

<h1>{counter}€</h1>
