<script>
  import { range } from "d3-array";
  import Drawings from "../chart_components/Drawings.svelte";
  import { radiansToDegrees } from "../utils/helpers";
  import { scaleLinear } from "d3-scale";

  export let year;
  export let itemWidth;
  export let itemHeight;
  export let months;
  export let monthScale;
  export let drawings;
  export let maxDrawings;
  console.log(year, drawings);

  const padding = 15;
  $: radius = (itemHeight - 4 * padding) / 2;

  $: radialScale = scaleLinear().domain([0, maxDrawings]).range([0, radius]);
</script>

<g transform="translate({itemWidth / 2}, 0)">
  <g transform="translate(0, {padding + radius})">
    <circle cx="0" cy="0" r={radius} fill="none" stroke="black" />
    {#each months as month, i}
      <line
        class="line-{month}-{i}"
        x1="0"
        y1="0"
        x2={radius * Math.sin(monthScale(i))}
        y2={-1 * radius * Math.cos(monthScale(i))}
      />
      <text
        class="month-label"
        transform="translate({(radius + 9) * Math.sin(monthScale(i))}, {-1 *
          (radius + 9) *
          Math.cos(monthScale(i))}) rotate({monthScale(i) <= Math.PI / 2 ||
        monthScale(i) >= (3 * Math.PI) / 2
          ? radiansToDegrees(monthScale(i))
          : radiansToDegrees(monthScale(i - 6))})"
        text-anchor="middle"
        dominant-baseline="middle">{month.slice(0, 3)}</text
      >
    {/each}
    <Drawings {drawings} {monthScale} {radialScale} />
  </g>
  <text y={itemHeight - 10} text-anchor="middle">{year}</text>
</g>

<style lang="scss">
  line {
    stroke: $text;
  }
  .month-label {
    font-size: 1.3rem;
  }
</style>
