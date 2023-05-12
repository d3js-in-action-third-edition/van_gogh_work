<script>
  export let letters;
  export let monthScale;
  export let radialScale;
  export let months;
  export let year;
</script>

{#each months as month, i}
  <line
    x1={0}
    y1={0}
    x2={radialScale(
      letters.find((l) => l.year === year && l.month === month)
        .number_of_letters
    ) * Math.sin(monthScale(i))}
    y2={-1 *
      radialScale(
        letters.find((l) => l.year === year && l.month === month)
          .number_of_letters
      ) *
      Math.cos(monthScale(i))}
    stroke-width={2.5}
    stroke-linecap="round"
  />
{/each}

<style lang="scss">
  line {
    stroke: $secondary;
    pointer-events: none;
  }
</style>
