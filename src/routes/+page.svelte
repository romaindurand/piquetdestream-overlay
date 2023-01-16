<script>
	import '../style.css';
	import { page } from '$app/stores';
	const name = $page.url.searchParams.get('name');
</script>

<span class="name">{name}</span>

<style>
	.name {
		color: red;
	}
</style>
