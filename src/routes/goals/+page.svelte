<script>
	let donations = 29000;
	/**
	 * @typedef Goal
	 * @type {Object}
	 * @property {string} text
	 * @property {number} value
	 */

	/**
	 * @type Goal[]
	 */
	let goals = [
		{
			text: 'objectif 1',
			value: 30000
		},
		{
			text: 'objectif 2',
			value: 31000
		},
		{
			text: 'objectif 3',
			value: 32000
		},
		{
			text: 'objectif 1.5',
			value: 29500
		}
	];

	$: percent = getPercent(donations, goals);

	/**
	 *
	 * @param {number} donations
	 * @param {Goal[]} goals
	 */
	function getPercent(donations, goals) {
		const sortedGoals = goals.sort((a, b) => a.value - b.value);
		const nextGoal = sortedGoals.find((goal) => goal.value > donations);
		const nextGoalIndex = sortedGoals.findIndex(goal => goal.value === nextGoal.value);
		const prevGoal = sortedGoals[nextGoalIndex - 1]

	}
</script>

<div class="wrapper">
	<div class="container">
		<div class="progress" style={`width: 50%;`} />
		<div class="next-goal-value">30000</div>
	</div>
</div>

<style>
	.wrapper {
		padding: 5px;
	}
	.container {
		position: relative;
		box-sizing: content-box;
		width: 100%;
		height: 30px;
		border: 2px solid black;
		border-radius: 3px;
		background-color: white;
	}

	.progress {
		position: absolute;
		left: 0;
		top: 0;
		background-color: blueviolet;
		border-right: 1px solid black;
		height: 100%;
	}

	.next-goal-value {
		font-family: 'Nerko One';
		position: absolute;
		right: 5px;
		top: 0;
		color: white;
		text-shadow: 0 0 2px black, 0 0 3px black, 0 0 4px black;
		font-size: 1.6em;
	}
</style>
