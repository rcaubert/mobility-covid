<script>
	import { onMount } from 'svelte';
	import { csv } from 'd3-fetch';
	import data from './data.js';

	import LineChart from './components/LineChart.svelte';

</script>

<main>
	<h1>Delhi slowly emerges out of lockdown.</h1>
		<div class='chart'>
			<LineChart data={data} />
			<p>Source: Google Mobility Report</p>
		</div>
</main>

<style>
	main {
		display: flex;
		flex-direction: column;
		align-items: center;
		text-align: center;
		max-width: 44rem;
		margin: 0 auto;
	}

	h1 {
		color: #ff3e00;
		font-size: 2em;
		font-weight: 100;
	}

	.chart {
		width: 100%;
		position: relative;
		margin-bottom: 1rem;
	}


</style>
