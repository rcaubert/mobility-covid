<script>
	import { onMount } from 'svelte';
	import { csv } from 'd3-fetch';
	import data from './data.js';

	import LineChart from './components/LineChart.svelte';

</script>

<main>
	<h1>Delhi in slow motion</h1>
	<p>Mobility trend for public transport hubs such as subway, bus, and train stations.
	Median by day of week from January 3rd-February 6th 2020 in Delhi, India.</p>
		<div class='chart'>
			<LineChart data={data} />
			<p>Source: <a href="https://www.google.com/covid19/mobility/" target="_blank">Google Mobility Reports</a></p>
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

	p {
		font-size: 1rem;
	}

</style>
