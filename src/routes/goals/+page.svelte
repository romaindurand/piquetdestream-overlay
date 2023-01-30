<script>
	import { onMount } from 'svelte';
	import goals from './goals.js';

	let donations = 0;

	$: ({ percent, nextGoal } = updateNextGoalData(donations, goals));

	onMount(async () => {
		const response = await window.fetch('https://piquetdestream-api.fly.dev/v1/counter/state');
		const data = await response.json();
		donations = Number.parseFloat(data.amount);
		const evtSource = new EventSource('https://piquetdestream-api.fly.dev/v1/counter/sse');
		evtSource.addEventListener('counter-update', handleEvent);
	});

	function handleEvent(event) {
		const data = JSON.parse(event.data);
		donations = Number.parseFloat(data.amount);
	}

	/**
	 * @param {number} donations
	 */
	function updateNextGoalData(donations) {
		const sortedGoals = goals.sort((a, b) => a.value - b.value);
		const nextGoal = sortedGoals.find((goal) => goal.value > donations);
		const nextGoalIndex = sortedGoals.findIndex((goal) => goal.value === nextGoal.value);
		const prevGoal = sortedGoals[nextGoalIndex - 1] || { value: 0 };
		const percent = ((donations - prevGoal.value) / (nextGoal.value - prevGoal.value)) * 100;
		return { percent, nextGoal };
	}
</script>

<div class="wrapper">
	<div class="container">
		<div class="progress" style={`width: ${percent}%;`} />
		<div class="text-content">
			<div class="next-goal-title">{nextGoal.text}</div>
			<div class="next-goal-value">{nextGoal.value}</div>
		</div>
	</div>
</div>

<style>
	.wrapper {
		padding: 5px;
	}
	.container {
		position: relative;
		overflow: hidden;
		width: 100%;
		height: 30px;
		border: 2px solid black;
		border-radius: 10px;
		background-color: white;
	}

	.progress {
		position: absolute;
		background-color: #9a0022;
		border-right: 1px solid red;
		height: 100%;
		transition: width 1s ease-in-out;
	}

	.text-content {
		box-sizing: border-box;
		padding-left: 10px;
		padding-right: 10px;
		width: 100%;
		position: absolute;
		display: flex;
		justify-content: space-between;
	}

	.next-goal-value {
		font-family: 'Nerko One';
		color: white;
		text-shadow: 0 0 2px black, 0 0 3px black, 0 0 4px black;
		font-size: 1.6em;
	}

	.next-goal-title {
		font-family: 'Nerko One';
		color: white;
		text-shadow: 0 0 2px black, 0 0 3px black, 0 0 4px black;
		font-size: 1.4em;
	}
</style>
