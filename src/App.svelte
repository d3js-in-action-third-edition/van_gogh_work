<script>
  import Header from "./layout/Header.svelte";
  import Grid from "./layout/Grid.svelte";
  import Timeline from "./layout/Timeline.svelte";
  import Notes from "./layout/Notes.svelte";
  import Sources from "./layout/Sources.svelte";
</script>

<Header />
<main>
  <div class="container">
    <section>
      <div class="row">
        <div class="col-2">
          <Timeline />
        </div>
        <div class="col-10">
          <Grid />
        </div>
      </div>
    </section>
    <section>
      <Notes />
      <Sources />
    </section>
  </div>
</main>
