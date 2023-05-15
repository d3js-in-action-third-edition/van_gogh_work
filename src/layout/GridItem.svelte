<script>
  import Drawings from "../chart_components/Drawings.svelte";
  import Paintings from "../chart_components/Paintings.svelte";
  import Letters from "../chart_components/Letters.svelte";
  import { radiansToDegrees } from "../utils/helpers";
  import { scaleLinear } from "d3-scale";
  import letters from "../data/letters.json";
  import { isYearIncluded } from "../utils/helpers";
  import { isMonthIncluded } from "../utils/helpers";

  export let year;
  export let itemWidth;
  export let itemHeight;
  export let months;
  export let monthScale;
  export let drawings;
  export let maxDrawings;
  export let paintings;
  export let maxPaintingArea;
  export let isTooltipVisible = false;
  export let tooltipMeta = {};
  export let isPeriodSelected;
  export let selectedPeriod;

  const padding = 30;
  $: radius = (itemWidth - 4 * padding) / 2;

  $: radialScale = scaleLinear()
    .domain([0, maxDrawings])
    .range([0, 2 * radius]);

  let isWorkTooltipVisible = false;
  const handleMouseEnter = () => {
    isWorkTooltipVisible = true;
  };
  const handleMouseLeave = () => {
    isWorkTooltipVisible = false;
  };
</script>

<g transform="translate({itemWidth / 2}, 0)">
  <g transform="translate(0, {2 * padding + radius})">
    <circle
      cx="0"
      cy="0"
      r={radius}
      fill="transparent"
      on:mouseenter={() => handleMouseEnter()}
      on:mouseleave={() => handleMouseLeave()}
    />
    <Drawings
      {drawings}
      {monthScale}
      {radialScale}
      {year}
      {isPeriodSelected}
      {selectedPeriod}
    />
    <Letters
      {letters}
      {monthScale}
      {radialScale}
      {months}
      {year}
      {isPeriodSelected}
      {selectedPeriod}
    />
    {#each months as month, i}
      <line
        class="line-{month}-{i}"
        class:lessen={isPeriodSelected &&
          !isMonthIncluded(
            selectedPeriod,
            months.findIndex((m) => m === month),
            year
          )}
        x1="0"
        y1="0"
        x2={radius * Math.sin(monthScale(i))}
        y2={-1 * radius * Math.cos(monthScale(i))}
        stroke-opacity={0.2}
        stroke-linecap="round"
      />
      <text
        class="month-label"
        class:lessen={isPeriodSelected &&
          !isMonthIncluded(
            selectedPeriod,
            months.findIndex((m) => m === month),
            year
          )}
        transform="translate({(radius + 30) * Math.sin(monthScale(i))}, {-1 *
          (radius + 30) *
          Math.cos(monthScale(i))}) rotate({monthScale(i) <= Math.PI / 2 ||
        monthScale(i) >= (3 * Math.PI) / 2
          ? radiansToDegrees(monthScale(i))
          : radiansToDegrees(monthScale(i - 6))})"
        text-anchor="middle"
        dominant-baseline="middle">{month.slice(0, 3)}</text
      >
      <text
        class="work-tooltip"
        class:visible={isWorkTooltipVisible}
        transform="translate({(radius + 48) * Math.sin(monthScale(i))}, {-1 *
          (radius + 48) *
          Math.cos(monthScale(i))}) rotate({monthScale(i) <= Math.PI / 2 ||
        monthScale(i) >= (3 * Math.PI) / 2
          ? radiansToDegrees(monthScale(i))
          : radiansToDegrees(monthScale(i - 6))})"
        text-anchor="middle"
        dominant-baseline="middle"
      >
        {paintings.filter((p) => p.month === month && p.year === year).length}p,
        {drawings.find((d) => d.month === month).drawings.length}d,
        {letters.find((l) => l.month === month && l.year === year)
          .number_of_letters}l
      </text>
      <Paintings
        {paintings}
        {monthScale}
        {radius}
        {maxPaintingArea}
        {itemWidth}
        bind:isTooltipVisible
        bind:tooltipMeta
        {isPeriodSelected}
        {selectedPeriod}
      />
    {/each}
  </g>
  <text
    class="year-label"
    y={itemHeight}
    text-anchor="middle"
    class:lessen={isPeriodSelected && !isYearIncluded(selectedPeriod, year)}
    >{year}</text
  >
</g>

<style lang="scss">
  line {
    stroke: $text;
    &.lessen {
      opacity: 0;
    }
  }
  .month-label,
  .work-tooltip {
    font-size: 1.4rem;
  }
  .work-tooltip {
    opacity: 0;
    transition: opacity 200ms ease;
    &.visible {
      opacity: 1;
    }
  }
  line,
  .month-label,
  .year-label {
    transition: opacity 100ms ease;
  }
</style>
