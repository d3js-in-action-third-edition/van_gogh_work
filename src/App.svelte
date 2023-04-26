<script>
  import Header from "./layout/Header.svelte";
  import Grid from "./layout/Grid.svelte";
  import Notes from "./layout/Notes.svelte";
  import Sources from "./layout/Sources.svelte";
</script>

<Header />
<main>
  <div class="container">
    <Grid />
    <Notes />
    <Sources />
  </div>
</main>
