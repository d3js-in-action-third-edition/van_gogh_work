<script>
  import { range } from "d3-array";
  import { each } from "svelte/internal";

  import GridItem from "./GridItem.svelte";

  const years = range(1880, 1891);
  const padding = 30;
  const container = 1400;
  let windowWidth;
  $: svgWidth =
    windowWidth >= container
      ? container - 2 * padding
      : windowWidth - 2 * padding;
  $: numColumns = windowWidth > 900 ? 3 : windowWidth > 600 ? 2 : 1;
  $: numRows = Math.ceil(years.length / numColumns);
  const itemHeight = 300;
  $: itemWidth = svgWidth / numColumns;
  $: svgHeight = numRows * itemHeight;
</script>

<svelte:window bind:innerWidth={windowWidth} />

<div class="viz-container">
  {#if svgWidth}
    <svg width={svgWidth} height={svgHeight}>
      {#each years as year, i}
        <g
          transform="translate({(i % numColumns) * itemWidth}, {Math.floor(
            i / numColumns
          ) * itemHeight})"
        >
          <rect x="0" y="0" width={itemWidth} height={itemHeight} />
          <GridItem {year} {itemWidth} {itemHeight} />
        </g>
      {/each}
    </svg>
  {/if}
</div>

<style>
  svg {
    border: 1px solid magenta;
  }
  rect {
    fill: none;
    stroke: cyan;
  }
</style>
