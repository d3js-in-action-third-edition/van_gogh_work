<script>
  import { range, max } from "d3-array";
  import { scaleLinear } from "d3-scale";

  import GridItem from "./GridItem.svelte";
  import drawings from "../data/drawings.json";
  import paintings from "../data/paintings_subject-link.json";

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

  const months = [
    "January",
    "February",
    "March",
    "April",
    "May",
    "June",
    "July",
    "August",
    "September",
    "October",
    "November",
    "December",
  ];
  const monthScale = scaleLinear()
    .domain([0, months.length])
    .range([0, 2 * Math.PI]);

  const yearlyDrawings = [];
  years.forEach((year) => {
    const relatedDrawings = { year: year, months: [] };
    months.forEach((month) => {
      relatedDrawings.months.push({
        month: month,
        drawings: drawings.filter(
          (drawing) =>
            drawing.year === year.toString() && drawing.month === month
        ),
      });
    });
    yearlyDrawings.push(relatedDrawings);
  });

  const maxDrawings = max(yearlyDrawings, (d) =>
    max(d.months, (i) => i.drawings.length)
  );

  const maxPaintingArea = max(paintings, (d) => d.width_cm * d.height_cm);

  import Tooltip from "../UI/Tooltip.svelte";
  $: isTooltipVisible = false;
  $: tooltipMeta = {
    x: 0,
    y: 0,
    url: "",
    title: "",
    createdIn: "",
    date: "",
    medium: "",
    currentLocation: "",
    dimensions: "",
  };
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
          <GridItem
            {year}
            {itemWidth}
            {itemHeight}
            {months}
            {monthScale}
            {maxDrawings}
            drawings={yearlyDrawings.find((d) => d.year === year).months}
            paintings={paintings.filter((p) => +p.year === year)}
            {maxPaintingArea}
            bind:isTooltipVisible
            bind:tooltipMeta
          />
        </g>
      {/each}
    </svg>
  {/if}
  {#if isTooltipVisible}
    <Tooltip
      x={tooltipMeta.x}
      y={tooltipMeta.y}
      url={tooltipMeta.url}
      title={tooltipMeta.title}
      createdIn={tooltipMeta.createdIn}
      date={tooltipMeta.date}
      medium={tooltipMeta.medium}
      currentLocation={tooltipMeta.currentLocation}
      dimensions={tooltipMeta.dimensions}
    />
  {/if}
</div>

<style>
  .viz-container {
    position: relative;
  }
  svg {
    border: 1px solid magenta;
  }
  rect {
    fill: none;
    stroke: cyan;
  }
</style>
