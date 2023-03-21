<script>
	import { onMount, tick } from 'svelte';
	import { fade } from 'svelte/transition';

	let notifications = [];
	let showNotification = false;
	let notificationName = '';
	let notificationMessage = '';

	$: {
		if (notifications.length > 0) {
			dequeueNotification();
		}
	}

	async function dequeueNotification() {
		if (showNotification) return;
		const last = notifications[0];
		const { amount, name, message } = JSON.parse(last.data); //TODO : update according to actual data structure

		notificationMessage = message;
		notificationName = name
			? `${name} vient de donner ${amount} € !`
			: `Un·e camarade vient de donner ${amount} € !`;
		showNotification = true;
		await tick();
		await sleep(10);
		showNotification = false;
		await tick();
		await sleep(0.5);
		notifications.shift();
		notifications = [...notifications];
	}

	async function sleep(s) {
		return new Promise((resolve) => {
			window.setTimeout(resolve, s * 1000);
		});
	}

	onMount(() => {
		const evtSource = new EventSource('https://piquetdestream-api.fly.dev/v1/counter/sse');
		evtSource.addEventListener('new-donation', handleEvent);
		window.ping = handleEvent;
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
	<div class="wrapper">
		<div class="nameplate" transition:fade>
			<div class="tick" />
			<h1>{notificationName}</h1>
		</div>
		{#if notificationMessage}<div class="message">{notificationMessage}</div>{/if}
	</div>
{/if}

<!-- {#if import.meta.env.MODE === 'development'}
	<pre>
	{JSON.stringify(notifications, null, 2)}
</pre>
{/if} -->
<style>
	/* pre {
		text-align: left;
	} */
	.wrapper {
		display: flex;
		flex-direction: column;
		position: relative;
	}

	.nameplate {
		position: relative;
		margin: 20px 0 0 0;
		padding: 20px 50px;
		font-weight: normal;
		color: #9a0022;
		border: solid #9a0022 8px;
		border-radius: 40px 40px 40px 40px;
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
		top: -19px;
		background: white;
		margin: auto;
		border-left: solid #9a0022 8px;
		border-right: solid transparent 8px;
		border-bottom: solid transparent 8px;
		border-top: solid #9a0022 8px;
		z-index: 1;
		display: block;
	}

	.message {
		font-family: cursive;
		font-size: 1.5em;
		font-weight: normal;
		color: white;
		text-shadow: 2px 0 5px black, -2px 0 5px black, 0 2px 5px black, 0 -2px 5px black;
		text-align: center;
		margin: 20px 0 0 0;
		word-break: break-word;
	}
</style>
