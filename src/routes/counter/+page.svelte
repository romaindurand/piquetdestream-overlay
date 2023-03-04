<script>
	import { onMount } from 'svelte';

	let counter = '0.00';
	let integerValue, decimalValue;
	$: {
		[integerValue, decimalValue] = String(counter).split('.');
	}

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

<h1>
	<span class="integer" style={`--num: ${integerValue};`} /><span>.{decimalValue}€</span>
</h1>

<style>
	h1 {
		font-family: 'Crossten';
		font-size: 120px;
		color: #000;
	}

	@property --num {
		syntax: '<integer>';
		initial-value: 0;
		inherits: false;
	}

	span.integer {
		transition: --num 5s;
		counter-set: num var(--num);
	}

	span.integer::after {
		content: counter(num);
	}
</style>
