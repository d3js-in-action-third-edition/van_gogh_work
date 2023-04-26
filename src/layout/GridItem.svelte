<script>
  export let year;
  export let itemWidth;
  export let itemHeight;

  const padding = 15;
  $: radius = (itemHeight - 4 * padding) / 2;
</script>

<g transform="translate({itemWidth / 2}, 0)">
  <g transform="translate(0, {padding + radius})">
    <circle cx="0" cy="0" r={radius} />
  </g>
  <text y={itemHeight - 20} text-anchor="middle">{year}</text>
</g>
