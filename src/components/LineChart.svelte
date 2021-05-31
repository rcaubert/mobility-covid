<script>
    import { fade } from 'svelte/transition';
    //import { transition } from 'd3-transition';
    import { line } from 'd3-shape';
    import { timeFormat } from 'd3-time-format';
    import { scaleTime, scaleLinear } from 'd3-scale';
    import { extent, bisector } from 'd3-array';

    import Axis from './Axis.svelte';
    import Axes from './Axes.svelte';

    export let data;

    // chart dimensions
    const height = 400;
    const margin = { top: 20, right: 20, bottom: 40, left: 25 };
    const marg = 40;

    let width;
    let extentX = extent(data, d => new Date(d.date));
    let extentY = extent(data, d => +d.transit_stations);

    // scales
    $: xScale = scaleTime()
            .domain(extentX)
            .range([margin.left, width - margin.right]);

    $: yScale = scaleLinear()
            .domain(extentY)
            .range([height - margin.bottom, margin.top]);

    // line
    $: path = `M${data.map(d => `${xScale(new Date(d.date))},${yScale(+d.transit_stations)}`).join('L')}`;
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

</script>

<div bind:clientWidth={width}>
    {#if width}
        <svg width={width} height={height}>
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
        </svg>
    {/if}
</div>
