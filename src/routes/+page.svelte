<script>
	import '../base.css';
	import { onMount } from 'svelte';
	import { fade } from 'svelte/transition';

	let notifications = [];
	let showNotification = false;
	let notificationMessage = '';

	console.log(import.meta.env.MODE);

	$: {
		if (notifications.length > 0 && !showNotification) {
			const last = notifications[0];
			const { amount, name } = JSON.parse(last.data); //TODO : update according to actual data structure
			notificationMessage = `${name} vient de donner ${amount} € !`;
			showNotification = true;
			window.setTimeout(() => {
				showNotification = false;
				notifications.shift();
				notifications = [...notifications];
			}, 5000);
		}
	}

	onMount(() => {
		const evtSource = new EventSource('https://piquetdestream-api.fly.dev/v1/counter/sse', {
			withCredentials: true
		});

		evtSource.addEventListener('new-donation', logEvent);
		window.ping = logEvent;
	});

	function logEvent(event) {
		console.dir({ event }, { depth: null });
		notifications = [...notifications, event];
	}
</script>

<svelte:head>
	<link
		href="https://fonts.googleapis.com/css2?family=M+PLUS+Rounded+1c:wght@500&amp;family=Nerko+One&amp;display=swap"
		rel="stylesheet"
	/>
</svelte:head>

{#if showNotification}
	<div class="nameplate" transition:fade>
		<div class="tick" />
		<h2>{notificationMessage}</h2>
	</div>
{/if}

{#if import.meta.env.MODE === 'development'}
	<pre>{JSON.stringify(notifications, null, 2)}</pre>
{/if}

<style>
	pre {
		text-align: left;
	}
	.nameplate {
		position: relative;
		font-family: 'Nerko One';
		margin: 20px 0 0 0;
		padding: 0 50px;
		font-weight: normal;
		color: #19ba9b;
		border: solid #19ba9b 8px;
		border-radius: 40px 40px 40px 10px;
		display: inline-block;
		min-width: 200px;
		text-align: center;
		font-size: 2em;
		background: white;
	}

	h2 {
		display: inline;
		font-size: 2em;
		font-weight: normal;
		z-index: 100;
	}

	.tick {
		width: 20px;
		height: 20px;
		transform: rotate(45deg);
		position: absolute;
		right: 15%;
		top: -18px;
		background: white;
		margin: auto;
		border-left: solid #19ba9b 8px;
		border-right: solid transparent 8px;
		border-bottom: solid transparent 8px;
		border-top: solid #19ba9b 8px;
		z-index: 1;
		display: block;
	}
</style>
