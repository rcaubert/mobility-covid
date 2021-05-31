<script>
    import { fade } from 'svelte/transition';

    export let type = 'bottom';
    export let translate = 'translate(0, 0)';
    export let x = 0;
    export let x1 = 0;
    export let x2 = 0;
    export let y = 4;
    export let y1 = 0;
    export let y2 = 0;
    export let dasharray = 2;
    export let text;

    let g;
    let count;
    let transform;

</script>

{#if type == 'bottom'}
    <g class='tick' transform='{translate}'>
        <text y='{y}' style='text-anchor:middle;'>{text}</text>
    </g>
{:else}
    <g class='tick' transform='{translate}'>
        <line x1='{x1}' x2='{x2}' stroke-dasharray='{dasharray}'/>
        <text x='{x}' y='{y}' style='text-anchor:end;'>{text}</text>
    </g>
{/if}

<style>
    text {
        font-size: 0.8rem;
        fill: #595959;
    }

    .tick line {
        stroke: #000;
        opacity: 0.6;
    }
</style>
