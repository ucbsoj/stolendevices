<script lang="ts">
  import rawBlocks from '$lib/doc.blocks.json';
  import { asset } from '$app/paths';
  import DocRenderer from '$lib/components/DocRenderer.svelte';
  import type { Block } from '$lib/components/DocRenderer.svelte';
  const blocks = rawBlocks as Block[];
</script>



<!-- 
Hard-code custom code that should appear BEFORE Google Doc here. 
This code is in +page.svelte and can be manually coded using HTML or Svelte tags.
-->
<header class="hero container-fluid position-relative d-flex flex-column align-items-center justify-content-center vh-100">
  <h1 class="text-white text-center display-1">Across The Ocean</h1>
  <p class="text-white fw-light text-center">From the Bay Area to Vietnam: Follow the journey of stolen mobile devices</p>
  <video width="100%" height="100%" muted autoplay playsinline loop>
    <source src={asset('/hero-video.mp4')} type="video/mp4">
  </video>
</header>


<!-- DO NOT EDIT (except to modify classes). This will become code imported from Google Doc. -->
<div class="container">
  <div class="row justify-content-center">
    <div class="col-12 col-sm-10 col-lg-8 col-xxl-6" style="margin-top:-16px;">
      <DocRenderer {blocks} />
    </div>
  </div>
</div>


<!-- Hard-code any custom code that should appear AFTER Google Doc below here. -->
<footer class="container-fluid bg-dark text-white p-5">
    <p class="crimson text-center">UC Berkeley Journalism</p>
</footer>