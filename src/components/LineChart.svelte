<script>
    import { fade } from 'svelte/transition';
    //import { transition } from 'd3-transition';
    import { line } from 'd3-shape';
    import { timeFormat } from 'd3-time-format';
    import { scaleTime, scaleLinear } from 'd3-scale';
    import { extent, min, max } from 'd3-array';

    import Axis from './Axis.svelte';
    import Axes from './Axes.svelte';

    export let data;

    // chart dimensions
    const height = 400;
    const margin = { top: 20, right: 20, bottom: 40, left: 25 };
    const marg = 40;

    let width;
    let extentX = extent(data, d => new Date(d.date));
    console.log("extentX", extentX);
    let extentY = extent(data, d => +d.transit_stations);
    console.log("extentY", extentY);

    // scales
    $: xScale = scaleTime()
            .domain(extentX)
            .range([margin.left, width - margin.right]);

    $: yScale = scaleLinear()
            .domain(extentY)
            .range([height - margin.bottom, margin.top]);

    // line
    $: pathGenerator = line()
                    .x(d => xScale(new Date(d.date)))
                    .y(d => yScale(+d.transit_stations));

    // test
    $: minX = new Date(data[0].date);
    $: maxX = new Date(data[data.length - 1].date);
    $: path = `M${data.map(p => `${xScale(new Date(p.date))},${yScale(+p.transit_stations)}`).join('L')}`;
    $: area = `${path}L${xScale(maxX)},${yScale(0)}L${xScale(minX)},${yScale(0)}Z`;

    // ticks
    $: xTicks = width > 360 ? ['March 2020', 'June 2020', 'September 2020', 'December 2020', 'March 2021', 'May 2021'] : ['Mar 2020', 'Dec 2020', 'May 2021'];

    //$: xTicks = ['Mar 2020', 'April 2020', 'May 2020', 'June 2020', 'July 2020', 'August 2020', 'September 2020', 'October 2020', 'November 2020', 'December 2020', 'Jan 2021', 'February 2021', 'March 2021', 'April 2021', 'May 2021'];

    $: yTicks = height > 180 ?
        [0, -10, -20, -30, -40, -50, -60, -70, -80] :
        [0, -20, -40, -60, -80];

    const formatTick = (tick) => {
        const format = timeFormat("%B %Y");
        let ret = format(new Date(tick));
        return (ret);
    }

</script>

<div bind:clientWidth={width}>
    {#if width}
        <svg width={width} height={height}>
            <Axes {width} {height} margin={marg} scale={xScale} position='bottom' />
            <Axes {width} {height} margin={marg} scale={yScale} position='left' />
            <path
                d={pathGenerator(data)}
                fill='none'
                stroke='salmon'
                stroke-width={2}
                stroke-linecap='round'
                in:fade
                out:fade
            />
            <path
                class='path-area'
                d={area}
                fill='salmon'
                opacity=0.4
                in:fade
                out:fade
            />
        </svg>
        <svg width={width} height={height}>
            <!-- y axis -->
            <g class='axis y-axis'>
                {#each yTicks as tick}
                    <Axis
                        type='left'
                        translate='translate(6, {yScale(tick)})'
                        x1='{xScale(extentX[0])}'
                        x2='{xScale(extentX[1])}'
                        x='{margin.left - 6}'
                        y='+2'
                        text={`${tick}%`}
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
                        y1='{yScale(extentY[0]) - 16}'
                        y2='{yScale(extentY[1]) - 16}'
                        y='{height - margin.bottom + 16}'
                        text={tick}
                        dasharray=2>
                    </Axis>
                {/each}
            </g>
            <path
                d={pathGenerator(data)}
                fill='none'
                stroke='salmon'
                stroke-width={2}
                stroke-linecap='round'
                in:fade
                out:fade
            />
            <path
                class='path-area'
                d={area}
                fill='salmon'
                opacity=0.4
                in:fade
                out:fade
            />
        </svg>
    {/if}
</div>
