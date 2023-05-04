<script>
  import { forceSimulation, forceX, forceY, forceCollide } from "d3-force";

  export let paintings;
  export let monthScale;
  export let radius;

  const nodeRadius = 3;

  // $: {
  //   forceSimulation()
  //     .force(
  //       "x",
  //       forceX((d) => radius * Math.sin(monthScale(d.monthIndex))).strength(0.5)
  //     )
  //     .force(
  //       "y",
  //       forceY(
  //         (d) => -1 * radius * Math.cos(monthScale(d.monthIndex))
  //       ).strength(0.5)
  //     )
  //     .force("collide", forceCollide().radius(nodeRadius + 1))
  //     .nodes(paintings);
  //   // .on("tick", updateNetwork);
  // }

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
          d.monthIndex === 0 ? 0 : radius * Math.sin(monthScale(d.monthIndex))
        ).strength(0.5)
      )
      .force(
        "y",
        forceY((d) =>
          d.monthIndex === 0
            ? 0
            : -1 * radius * Math.cos(monthScale(d.monthIndex))
        ).strength(0.5)
      )
      .force(
        "collide",
        forceCollide()
          .radius(nodeRadius + 1)
          .strength(1)
      )
      .alpha(1); // [0, 1] The rate at which the simulation finishes. You should increase this if you want a faster simulation, or decrease it if you want more "movement" in the simulation.
    // .alphaDecay(0.0005); // [0, 1] The rate at which the simulation alpha approaches 0. you should decrease this if your bubbles are not completing their transitions between simulation states.
    // .restart();
  }
</script>

{#each nodes as node}
  <circle cx={node.x} cy={node.y} r={nodeRadius} />
{/each}
<!-- {#each paintings as painting}
  <circle cx={painting.x} cy={painting.y} r={nodeRadius} />
{/each} -->
