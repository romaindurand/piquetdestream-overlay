<script>
	import '../style.css';
	import { page } from '$app/stores';
	const name = $page.url.searchParams.get('name');
</script>

<svelte:head>
	<link
		href="https://fonts.googleapis.com/css2?family=M+PLUS+Rounded+1c:wght@500&amp;family=Nerko+One&amp;display=swap"
		rel="stylesheet"
	/>
</svelte:head>

<div class="nameplate">
	<div class="tick" />
	<h2 id="name">{name}</h2>
</div>

<style>
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
