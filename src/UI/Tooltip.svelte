<script>
  import { fly, fade } from "svelte/transition";
  import { subjects } from "../utils/subjects";

  export let x;
  export let y;
  export let url;
  export let title;
  export let createdIn;
  export let date;
  export let medium;
  export let currentLocation;
  export let dimensions;
  export let subject;
</script>

<div
  class="tooltip"
  style="left: {x + 10}px; top: {y + 10}px;"
  in:fly={{ y: 10, duration: 200, delay: 200 }}
  out:fade
>
  <div class="image">
    <a href={url} target="_blank" {title}><img src={url} {title} /></a>
  </div>
  <div class="metadata">
    <div
      class="subject"
      style="background-color: {subjects.find((s) => s.subject === subject)
        .color}"
    />
    <h3 class="title">{title}</h3>
    <div class="date">{createdIn}, {date}</div>
    <div class="medium">medium: {medium}</div>
    <div class="current-location">{currentLocation}</div>
    <div class="dimensions">{dimensions}</div>
  </div>
</div>

<style lang="scss">
  .tooltip {
    display: flex;
    position: absolute;
    background-color: $white;
    border-radius: $radius;
    box-shadow: 0px 2px 6px 0px rgba($text, 0.2);
  }
  .image {
    img {
      width: auto;
      height: 300px;
    }
  }
  a {
    display: block;
    height: 300px;
  }
  .metadata {
    position: relative;
    max-width: 600px;
    padding: 10px 30px 10px 25px;
  }
  .title {
    font-family: $fontSecondary;
  }
  .subject {
    position: absolute;
    top: 0;
    left: 0;
    width: 15px;
    height: 100%;
  }
</style>
