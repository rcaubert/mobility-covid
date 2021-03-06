<script>
    import { fade } from 'svelte/transition';
    import { line } from 'd3-shape';
    import { timeFormat } from 'd3-time-format';
    import { scaleTime, scaleLinear } from 'd3-scale';
    import { extent, bisector } from 'd3-array';
    import { select } from 'd3-selection';

    import Axis from './Axis.svelte';
    import Dot from './Dot.svelte';
    import Tooltip from './Tooltip.svelte';

    export let data;

    // chart dimensions
    const height = 400;
    const margin = { top: 20, right: 20, bottom: 40, left: 25 };

    let width;
    let extentX = extent(data, d => new Date(d.x));
    let extentY = extent(data, d => +d.y);

    // scales
    $: xScale = scaleTime()
            .domain(extentX)
            .range([margin.left, width - margin.right]);

    $: tScale = scaleLinear()
            .domain(['March 2020', 'May 2021'])
    	    .range([margin.left, width - margin.right]);

    $: yScale = scaleLinear()
            .domain(extentY)
            .range([height - margin.bottom, margin.top]);

    // line
    $: path = `M${data.map(d => `${xScale(new Date(d.x))},${yScale(+d.y)}`).join('L')}`;
    $: area = `${path}L${xScale(extentX[1])},${yScale(0)}L${xScale(extentX[0])},${yScale(0)}Z`;

    // ticks
    $: xTicks = width > 360
    ? ['March 2020', 'June 2020', 'September 2020', 'January 2021', 'May 2021']
    : ['March 2020', 'August 2020', 'January 2021', 'May 2021'];

    $: yTicks = [0, -10, -20, -30, -40, -50, -60, -70, -80];

    const formatTick = (tick) => {
        const format = timeFormat("%B");
        let ret = (tick === 'January 2021' ? 'January 2021' : format(new Date(tick)));
        return (ret);
    }

    // tooltip
    const formatTooltip = (x) => {
        const format = timeFormat("%B %d, %Y");
        let ret = format(new Date(x));
        return (ret);
    }

    const bisect = bisector((d) => {
        return (new Date(d.x));
    }).right;

	let m = { x: 0, y: 0 };
	let point = data[0];

	function handleMousemove(event) {
        m.x = event.offsetX;
        m.y = event.offsetY;

        let i = bisect(data, xScale.invert(m.x));

        if (i < data.length) {
          point = data[i];
        }
	}
    // vertical line
      let vline = {};
      $: vline.y1 = 0;
      $: vline.y2 = height - margin.bottom;
      $: vline.x1 = xScale(new Date(point.x));
      $: vline.x2 = xScale(new Date(point.x));

</script>

<div bind:clientWidth={width}>
    {#if width}
        <svg width={width} height={height} on:mousemove={handleMousemove}>
            <!-- y axis -->
            <g class='axis y-axis'>
                {#each yTicks as tick}
                    <Axis
                        type='left'
                        translate='translate(6, {yScale(tick)})'
                        x1={xScale(extentX[0])}
                        x2={xScale(extentX[1])}
                        x={tick === 0 ? margin.left + 82 : margin.left + 32}
                        y='-4'
                        text={tick === 0 ?`${tick}% (baseline)` : `${tick}%`}
                        dasharray= {tick === 0 ? 0 : 2}>
                    </Axis>
                {/each}
            </g>
            <!-- x axis -->
            <g class='axis x-axis'>
                {#each xTicks as tick}
                    <Axis
                        type='bottom'
                        translate='translate({xScale(new Date(tick))},0)'
                        y1={yScale(extentY[0]) - 16}
                        y2={yScale(extentY[1]) - 16}
                        y={height - margin.bottom}
                        text={formatTick(tick)}
                        dasharray=2>
                    </Axis>
                {/each}
            </g>
            <!-- area -->
            <path
                class='path-area'
                d={area}
                fill='#E75A36'
                opacity=0.4
                in:fade
                out:fade
            />
            <!-- line -->
            <path
                class="path-line"
                d={path}
                fill='none'
                stroke='#E75A36'
                stroke-width={2}
                stroke-linecap='round'
                in:fade
                out:fade
            />
            <!-- tooltip -->
            <Tooltip {vline} />
            <Dot x={xScale(new Date(point.x))} y={yScale(point.y)} val={point.y} day={formatTooltip(point.x)}/>
        </svg>
    {/if}
</div>
