<script>
  import { forceSimulation, forceX, forceY, forceCollide } from "d3-force";
  import { scaleRadial, scaleOrdinal } from "d3-scale";
  import { subjects } from "../utils/subjects";

  export let paintings;
  export let monthScale;
  export let radius;
  export let maxPaintingArea;
  export let itemWidth;
  export let isTooltipVisible = false;
  export let tooltipMeta = {};

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
            ? -1 * (radius + 80)
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
      .alpha(1); // [0, 1] The rate at which the simulation finishes. You should increase this if you want a faster simulation, or decrease it if you want more "movement" in the simulation.
    // .alphaDecay(0.0005); // [0, 1] The rate at which the simulation alpha approaches 0. you should decrease this if your bubbles are not completing their transitions between simulation states.
    // .restart();
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
    &.watercolor {
      stroke: $text;
    }
    &.print {
      stroke: #4bbfa7;
    }
  }
</style>
