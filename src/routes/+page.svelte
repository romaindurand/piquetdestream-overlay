<script>
	import '../base.css';
	import { onMount } from 'svelte';
	import { fade } from 'svelte/transition';

	let notifications = [];
	let showNotification = false;
	let notificationMessage = '';

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
		const evtSource = new EventSource('https://piquetdestream-api.fly.dev/v1/counter/sse');
		evtSource.addEventListener('new-donation', handleEvent);
	});

	function handleEvent(event) {
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
		<h1>{notificationMessage}</h1>
	</div>
{/if}

<style>
	.nameplate {
		position: relative;
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
