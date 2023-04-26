<script>
  import Drawings from "../chart_components/Drawings.svelte";
  import { radiansToDegrees } from "../utils/helpers";

  export let year;
  export let itemWidth;
  export let itemHeight;
  export let months;
  export let yearScale;
  export let drawings;

  const padding = 15;
  $: radius = (itemHeight - 4 * padding) / 2;
</script>

<g transform="translate({itemWidth / 2}, 0)">
  <g transform="translate(0, {padding + radius})">
    <circle cx="0" cy="0" r={radius} fill="none" stroke="black" />
    <Drawings {year} {drawings} />
    {#each months as month, i}
      <line
        class="line-{month}-{i}"
        x1="0"
        y1="0"
        x2={-1 * radius * Math.sin(yearScale(i))}
        y2={-1 * radius * Math.cos(yearScale(i))}
      />
      <text
        class="month-label"
        transform="translate({-1 * radius * Math.cos(yearScale(i))}, {-1 *
          radius *
          Math.sin(yearScale(i))}) rotate({radiansToDegrees(yearScale(i))})"
        >{month.slice(0, 3)}</text
      >
    {/each}
  </g>
  <text y={itemHeight - 20} text-anchor="middle">{year}</text>
</g>

<style lang="scss">
  line {
    stroke: $text;
  }
</style>
