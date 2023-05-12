<script>
  import { lineRadial, curveCatmullRomClosed } from "d3-shape";

  export let drawings;
  export let monthScale;
  export let radialScale;

  const lineGenerator = lineRadial()
    .angle((d, i) => monthScale(i))
    .radius((d) => radialScale(d.drawings.length))
    .curve(curveCatmullRomClosed);
</script>

<path d={lineGenerator(drawings)} />

<style lang="scss">
  path {
    fill: rgba($secondary, 0.3);
  }
</style>
