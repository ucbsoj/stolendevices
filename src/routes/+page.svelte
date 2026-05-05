<script lang="ts">
  import rawBlocks from '$lib/doc.blocks.json';
  import { asset } from '$app/paths';
  import DocRenderer from '$lib/components/DocRenderer.svelte';
  import type { Block } from '$lib/components/DocRenderer.svelte';
  import PhoneTheftInfographic from '$lib/components/PhoneTheftInfographic.svelte';

  const blocks = rawBlocks as Block[];
</script>

<header class="hero container-fluid position-relative d-flex flex-column align-items-center justify-content-center vh-100" style="overflow: hidden;">
  <h1 class="text-white text-center display-1">Across The Ocean</h1>
  <p class="text-white fw-light text-center">Follow the journey of stolen devices from the Bay Area to Vietnam</p>
  <video width="100%" height="100%" muted autoplay playsinline loop>
    <source src={asset('/hero-video.mp4')} type="video/mp4">
  </video>
</header>

<PhoneTheftInfographic />

<div class="container">
  <div class="row justify-content-center">
    <div class="col-12 col-sm-10 col-lg-8 col-xxl-6" style="margin-top:-16px;">
      <DocRenderer {blocks} />
    </div>
  </div>
</div>

<footer class="container-fluid bg-dark text-white p-5">
  <p class="crimson text-center">UC Berkeley Journalism</p>
</footer>