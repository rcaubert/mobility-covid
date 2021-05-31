<script>
    export let x = 0;
    export let y = 0;
    export let val = 'value';
    export let day = 'date';
</script>

<g>
    <circle class='dot' cx={x} cy={y} r='4' fill='#E75A36' stroke='#121212' />
    <text x={x - 5} y={y} style='text-anchor:end;'>{val}%</text>
    <text x={x - 5} y='18' style='text-anchor:end;'>{day}</text>
</g>

<style>
    text {
        font-size: 0.8rem;
        fill: black;
        stroke: black;
    }
</style>
