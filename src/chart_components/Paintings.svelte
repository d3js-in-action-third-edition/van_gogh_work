<script>
  import { forceSimulation, forceX, forceY, forceCollide } from "d3-force";
  import { scaleRadial, scaleOrdinal } from "d3-scale";
  import { subjects } from "../utils/subjects";
  import { isMonthIncluded } from "../utils/helpers";

  export let paintings;
  export let monthScale;
  export let radius;
  export let maxPaintingArea;
  export let itemWidth;
  export let isTooltipVisible = false;
  export let tooltipMeta = {};
  export let isPeriodSelected;
  export let selectedPeriod;

  const circleRadiusScale = scaleRadial()
    .domain([0, Math.sqrt(maxPaintingArea)])
    .range([0, 8]);
  const defaultRadius = 3;

  let simulation = forceSimulation(paintings);
  let nodes = [];
  simulation.on("tick", () => {
    nodes = simulation.nodes();
  });

  $: {
    simulation
      .force(
        "x",
        forceX((d) =>
          d.monthIndex === null
            ? Math.random() * itemWidth - itemWidth / 2
            : radius * Math.sin(monthScale(d.monthIndex))
        ).strength(0.5)
      )
      .force(
        "y",
        forceY((d) =>
          d.monthIndex === null
            ? radius + 70
            : -1 * radius * Math.cos(monthScale(d.monthIndex))
        ).strength(0.5)
      )
      .force(
        "collide",
        forceCollide()
          .radius((d) =>
            d.width_cm === null
              ? defaultRadius + 1
              : circleRadiusScale(Math.sqrt(d.width_cm * d.height_cm)) + 1
          )
          .strength(1)
      )
      .alpha(0.5)
      .alphaDecay(0.5);
  }

  const colorScale = scaleOrdinal()
    .domain(subjects.map((d) => d.subject))
    .range(subjects.map((d) => d.color));

  const handleMouseEnter = (e, node) => {
    isTooltipVisible = true;
    tooltipMeta = {
      x: e.offsetX,
      y: e.offsetY,
      screenY: e.clientY,
      url: node.image,
      title: node.title,
      createdIn: node.created_in,
      date: node.month !== "" ? `${node.month} ${node.year}` : node.year,
      medium: node.medium,
      currentLocation: node.current_location,
      dimensions:
        node.width_cm !== null ? `${node.width_cm} x ${node.height_cm} cm` : "",
      subject: node.subject,
    };
  };
  const handleMouseLeave = () => {
    isTooltipVisible = false;
  };
</script>

{#each nodes as node}
  <circle
    class:watercolor={node.medium === "watercolor"}
    class:print={node.medium === "print"}
    class:lessen={isPeriodSelected &&
      !isMonthIncluded(selectedPeriod, node.monthIndex, node.year)}
    cx={node.x}
    cy={node.y}
    r={node.width_cm === null
      ? defaultRadius
      : circleRadiusScale(Math.sqrt(node.width_cm * node.height_cm))}
    fill={node.subject !== "" ? colorScale(node.subject) : "white"}
    on:mouseover={(e) => handleMouseEnter(e, node)}
    on:focus={(e) => handleMouseEnter(e, node)}
    on:mouseleave={() => handleMouseLeave()}
  />
{/each}

<style lang="scss">
  circle {
    cursor: pointer;
    stroke: $white;
    transition: opacity 100ms ease;
    &.watercolor {
      stroke: $text;
    }
    &.print {
      stroke: $secondaryPale;
    }
    &.lessen {
      fill-opacity: 0.1;
      stroke-opacity: 0.1;
    }
  }
</style>
